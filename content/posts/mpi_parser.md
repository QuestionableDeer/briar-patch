+++
title = "Something Nicer"
date = "2026-06-06T18:23:35-07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Briar"
#authorTwitter = "" #do not include @
#cover = ""
tags = ["Retro", "Projects", "C++", "MUCK"]
keywords = ["Parser", "Recap"]
description = "Come with me, and you'll see, a world of pure imagination!"
showFullContent = false
readingTime = false
hideComments = false
+++

I realize I keep bouncing between screeds and fun little project summaries like a meth-addled squirrel, but
I don't want everything I write to be depressing or angry. It would be far too easy to do that, in all honesty,
so I'm making myself look on the bright side.

In this case, you might remember me saying something about the flexibility of MUCK descriptions in my [post](../line_by_line)
about the line editor project. It went a bit like this:

>There's a level of immersion to MUCKs that you don't get elsewhere because the systems were designed to be
player-extensible. You want to set up a tavern with multiple rooms, a bar, an NPC bartender, and automated seasonal events? It's yours, if you
spend some time to learn the built-in scripting/markup language the server knows how to interpret. Once it's done, anyone else on the server can
visit, buy a drink, explore the rooms, and interact with other customers (human or NPC).

At the time, I didn't comment on how exactly those sorts of complex, dynamic effects were achieved. All I said
was that doing it required some decently heavy text editing, and how that need motivated developing a line editing system
for MUCK users. This time, I'm going to dive in and show you exactly how the interactive scripting works, along with the parser I developed to
implement the idea for myself.

# MPI - No, the Other One

If you have a background in esoteric numerical computation, you might already know about one sort of MPI - the Message Passing Interface,
developed in the 90s to provide a uniform API for massively parallel scientific applications.

If you have a different kind of esoteria in your background, you'll recognize MPI as the Message Parsing Interpreter. This was also
developed in the 90s, but to a much different end - allowing players on MUCKs to spice up their environments and objects with dynamic
elements. This latter form of MPI basically amounts to a markup language that permits users to perform some limited computations and
apply Boolean predicates to parts of their descriptions. The upshot is that you can do things like dynamically update the list of people in a
room, or change the weather at random intervals.

So what does that actually look like? Here's a simple example: `You see a doe {if:{contains:jacket,me},wearing a cool bomber jacket.,in her birthday suit!}`. 
If my character happens to have a jacket in her inventory, you get the first description `You see a doe wearing a cool bomber jacket.`, otherwise
you get the second `You see a doe in her birthday suit!`.

While that's pretty basic, it doesn't take too much pondering to realize there's a whole lot more you can do with it, especially
if you happen to be a mod/admin who can create similar markup that affects other people's characters too. Cursed rings that turn players
to frogs and change their stats, a whole closet of outfits, a magic mirror that can change your gender or species, or even an NPC
with a complete dialogue tree... 

Combine some imagination and enough programming skill to use these tags and you can get impressive mileage.

# Down the Rabbit Hole

Obviously, I could content myself with fancy little descriptions and neat objects on an existing MUCK, but for a project I decided to build
my own parser for the MPI language itself. This is appealing for a couple reasons:

1. The entire language is already [documented](https://www.rdwarf.com/users/mink/muckman/mpiref.html)
2. Most use cases are for fairly simple and short segments of code, typically fitting on a single line
3. The structure is mostly defined by brackets and commas, so the parsing rules are straightforward

The secret fourth reason is that I've been meaning to go through the Dragon Book on compilers for a while, but I needed a project
to motivate me into starting with it. As it turns out, MPI happens to be a pretty good example of the syntax-directed translation
that book gets so excited over, where you can build an implicit parse tree via recursion and call out to your own functions to implement 
the various language constructs.

The only caveat is that I didn't want to implement an entire MUCK engine behind it (yet), so most of the DB-related commands
and some of the object-interaction ones will have to wait until I tackle that backlog item first.

# Building a Parser

Unlike the line editor example, I was aware from the first that I was going to need *extensive* tests to make sure I stayed
on track. Partially that's because I've dabbled with parsers and compilers some before - I worked through [Nand2Tetris](https://www.nand2tetris.org/)
and followed along with the first half of [Crafting Interpreters](https://craftinginterpreters.com/). Both experiences
drove home the importance of torture testing with this kind of project.

I think I managed to live up to my goal too. At the current stopping point I have about 760 lines of C++ code for the main parser,
with nearly twice that in tests, and those tests did catch things!

Interestingly, writing the tests and playing with them did more to jumpstart the design region of my brain than the debugging one. The
biggest place they helped me was when I went to implement some of the looping constructs and realized while working on the test cases that
I couldn't eagerly recurse on arguments that were also function calls - for something like a `for` loop, I needed to be able to set up a 
local variable and change its value on every execution of the block.

In the end, I rewrote my argument parsing to be simpler and to pass around unevaluated blocks, at the cost of introducing some brace-counting tedium.
At some point elbow-deep in braces, I also realized how much the language design was influenced by LISP, which makes a lot of sense - the other language
MUCKs support is Forth, and both were big academic languages in the 80s and 90s. If you were a CS student building little languages
for your game server back then, those would be the ones to reach for, since you probably knew the syntax and you might have had a course building
something like a LISP evaluator already.

Speculation aside, what does all this look like? 

It's actually pretty simple! The main parsing loop accepts a line of text, and then only really has to deal with three cases: either
the input contains a `{` to signal a function call, an `&` that indicates variable access, or it's just text and whitespace. Everything gets
passed through until the parser finds one of the first two symbols, and the variable access boils down to a lookup in a table, so the interesting
option is how the code handles function calls.

Once we detect that we have a function call, we need to determine which function we're dealing with and what its arguments are. Luckily,
all functions in MPI have the form `{<name>:<args>}` where `<args>` is a comma-separated list that can itself contain function calls, variable
accesses, etc.

(That's a slight overgeneralization, since some functions can have zero arguments and are written as `{name}`, but there's only a handful and our
argument parsing will handle them the same way.)

In my implementation, once we're parsing a function, we simply search for the `:` character, and then split out the name as a lookup key. The arguments 
are split up separately and stored in a vector, which makes the function implementation signatures all identical - they'll accept a vector of strings 
and return a single string that results from evaluating the function on its arguments. In turn, that means we can store the lookup table easily as a 
map of `(name, function pointer)` pairs.

```c++
using BuiltinSig =
    std::function<std::string(Parser &, std::vector<std::string>)>;

struct FnInfo {
    BuiltinSig fnRef;
    ssize_t minArity;
    ssize_t maxArity;
};

const std::unordered_map<std::string, FnInfo> m_knownFunctions = {
      {"abs", {&Parser::fn_abs, 1, 1}},
      {"add", {&Parser::fn_add, 2, ARG_MAX}},
      {"and", {&Parser::fn_and, 2, ARG_MAX}},
      {"commas", {&Parser::fn_commas, 1, 4}},
      /* more entries... */
      {"while", {&Parser::fn_while, 2, 2}},
      {"with", {&Parser::fn_with, 3, ARG_MAX}},
      {"xor", {&Parser::fn_xor, 2, 2}},
  };
```

The somewhat-convoluted `BuiltinSig` type alias just stands for a function taking a `Parser` reference and a vector of strings
and returning a single string - exactly as described above (modulo the usually-implicit `this` term that member functions require).
The `FnInfo` structure also includes arity limits since the MPI functions vary in the kinds of arguments they support - some behave like
LISP's arithmetic operators that can accept the traditional two inputs, or a whole sequence to reduce over. While the arity
is checked via a `[min, max]` interval, some functions don't support all arities in the range, but that's typically easy to handle
with some extra checking within the function implementations.

The process of splitting up the arguments to each function demands a little subtlety, since functions can nest. My approach is to
count brackets, and only consider a comma as an argument delimiter when the bracket count balances. As far as I can tell, the
language spec is simple enough that this is always correct, even if it might not work for a more general case.

That code is here in its entirety:

```c++
std::vector<std::string> Parser::split_args(std::string_view text, size_t pos) {
  std::vector<std::string> args;

  int braceDepth = 0;
  size_t start = pos;
  size_t idx = start;

  while (start < text.size() && idx < text.size()) {
    if (text[idx] == '{') {
      braceDepth++;
    } else if (text[idx] == '}') {
      braceDepth--;
    } else if (text[idx] == ',' && braceDepth == 0) {
      // found top-level arg break
      args.push_back(std::string{substr_exclusive(text, start, idx)});
      start = idx + 1;
    }

    idx++;
  }

  // catch any final section not comma-delimited
  if (start < text.size()) {
    args.push_back(std::string{substr_exclusive(text, start, idx)});
  }

  return args;
}
```

The `substr_exclusive` call is another one of those helper functions required because C++ hates intuitive string manipulation and I
don't want to litter the code with comments tracking whether it should be `end - start` or `end - start + 1` to do what I need:

```c++
std::string_view Parser::substr_exclusive(const std::string_view sv,
                                          size_t start, size_t end) {
  if (start > end) {
    report_error(ErrType::ERR_INVALID_RANGE, sv, start);
  }

  return sv.substr(start, end - start);
}
```

That's most of the general purpose code. The bulk of the methods within `Parser` are actually implementations of the functionality described
in the reference manual. For completeness, I'll post the code for the `for` loop construct I mentioned above as an example of those methods. Compare it to
the [spec](https://www.rdwarf.com/users/mink/muckman/mpiref.html#for).

```c++
std::string Parser::fn_for(std::vector<std::string> args) {
  auto const var = parse(args[0]);

  long const start = parse_long(parse(args[1]));
  long const end = parse_long(parse(args[2]));

  auto const &incRaw = args[3];
  long const inc = parse_long(parse(incRaw));
  if (inc == 0) {
    report_error(ErrType::ERR_INVALID_NUM, incRaw, 0);
  }

  // comparison logic changes for increment vs. decrement
  auto pred = [&end, &inc](long val) {
    return (inc >= 0) ? (val <= end) : (val >= end);
  };

  long i = start;
  auto const &command = args[4];
  while (pred(i)) {
    set_var_local(var, std::to_string(i));
    parse(command);
    i += inc;
  }

  unset_var_local(var);

  return {};
}
```

It's maybe a little ironic to implement `for` using a `while` loop, but with the slightly odd logic I needed to handle
counting either up or down, it felt like the predicate testing structure of a `while` was more natural when I was writing this one.

# Where Do We Go From Here?

After a point, the implementation of the various functions was starting to feel a little mechanical, and it seemed like I had
learned most of what I set out to regarding building a simple parser from scratch. Since I'm holding off on implementing an actual
MUCK engine, a lot of the more interesting commands weren't available anyway and I decided to pause development.

Even so, a neat idea occurred to me while I was working on this - what if you made a game that was a fusion of MPI coding and a solo
dungeon crawler? In particular, what if you could do something like cast spells as little chunks of MPI or LISP-like code, where
you could directly edit the spell properties to better exploit your enemies? Adding a fire tag to capitalize on a weakness on the fly, 
or pumping up the damage to fell a particularly strong foe? Of course, there needs to be some rock-paper-scissors so the solution 
to every fight isn't just setting `damage = 1000000000`, but familiar mechanics like magic barriers, immunities, etc. offer some 
handy building blocks to add flavor.

While I haven't actually started on this project yet, I think the goal would be something like a blend of a Zachtronics game with
*King's Field*. Of course, graphics are another story, but it might serve as a fun excuse to build a simple raytracer for
a DOOM aesthetic...

See how much fun you can have when you write programs for yourself instead of letting an LLM do it?
