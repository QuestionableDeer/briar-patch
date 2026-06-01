+++
title = "Line by line"
date = "2026-05-29T00:55:04-07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Briar"
#authorTwitter = "" #do not include @
#cover = ""
tags = ["Retro", "Projects", "C++", "Rust", "MUCK"]
keywords = ["Editor", "Recap"]
description = "Keep the old ways..."
showFullContent = false
readingTime = false
hideComments = false
+++

# Ancient History

Since I talked about building things last time, it's only fair I share the things I've been working on. A lot recently has been inspired by old tech.
Some of it is probably nostalgia for an era I didn't really get to experience, but some of it is genuine delight. Lately I've been excited by the
cutting edge of early-90s internet - the MUCK.

The hell is that? Well, it's a less grind-oriented variant of the multi-user dungeon (MUD).

Imagine something like a text game based on tabletop RPGs, except genuinely multiplayer as mediated by the same kind of systems used to run 
bulletin boards on the early internet. Something like a transitional form between *Rogue* and *Ultima Online*. Or for a modern analogy, what
a friend described as text-based VRChat.

The parallel to VR is striking, actually. There's a level of immersion to MUCKs that you don't get elsewhere because the systems were designed to be
player-extensible. You want to set up a tavern with multiple rooms, a bar, an NPC bartender, and automated seasonal events? It's yours, if you
spend some time to learn the built-in scripting/markup language the server knows how to interpret. Once it's done, anyone else on the server can
visit, buy a drink, explore the rooms, and interact with other customers (human or NPC). And since everything is text, the limit is just your
imagination - unlike VRC there's no need to buy fresh assets or spend hours retexturing things in Blender. Your first, last, and only tool is
a text editor.

Even more amazing to me is the fact that there are still a few of these servers running. Pre-World Wide Web gathering spots that continue to
attract a trickle of players even today.

# Weird Tech

About that text editor - the first, last, and only one? There's something a bit weird about it. You see, MUCK servers run off the telnet protocol
(or the TLS version these days), which means that their interface is line-oriented. When you press <kbd>Enter</kbd>, you're officially done with
your current input, and the total character count of a single input block is typically limited by the server.

So how do you get your descriptions and your markup onto the server? 

That's where the built-in text editor comes in. Like telnet, it's designed to be line-based, so there's no WYSIWYG interface or X-forwarding or
anything like that. But there *is* prior art.

It turns out that a similar problem existed in the early days of computing. Most input came from line-oriented terminals, which could be nothing
more than fancy electrified typewriters, and output was similiarly dumped to line printers (the same idea but in reverse, kind of a player-piano
analogue of a typewriter). "Scrollback" amounted to skimming back up the physical paper sheet that had extruded so far from your device, so 
managing multi-line input was a nontrivial task.

The solution winds up being the line editor, which continues down the decades as the `ed` editor in Unix. Once you start it up, you enter your
input one line at a time, but the program pastes each new line into a file somewhere, and when prompted, it can spit out the full text at you.
Editing commands are handled by marking the input with special characters, the popular MUCK option being to start command words with `.`, since
typically you won't be starting your sentence by ending it.

With commands to move around across existing lines, insert, delete, copy, move, and do basic find-and-replace, you can get some surprising mileage
out of an editor like this, typically looking like so:

```
~$ edit
Welcome! Use ".help" for command list.
<Inserting at line 1.>
> About that text editor - the first, last, and only one?
> There's something a bit weird about it.
> When you press <kbd>Enter</kbd>, you're officially done with your current input.
> .insert 3
<Inserting at line 3.>
> You see, MUCK servers run off the telnet protocol, which means their interface is line-oriented.
> .print
About that text editor - the first, last, and only one?
There's something a bit weird about it.
You see, MUCK servers run off the telnet protocol, which means their interface is line-oriented.
When you press <kbd>Enter</kbd>, you're officially done with your current input.
<Inserting at line 4.>
...
```

As it turns out, this kind of editor is pretty simple to program. It's not winning any awards for user-friendliness, but for a somewhat narrow domain
where users can be expected to have a degree of technical savvy (mostly regarding command line weirdness) it's a solid tradeoff - users get features
that would be painful to do in piecemeal manual batches and developers don't need to fuss with `ncurses` or GUI libraries or complex formatting and
presentation algorithms. From that standpoint, I think it's a really good example of what the best of internal tooling can be. While relatively few
software shops need line editors *per se*, being able to strike the right balance of efficiency gain vs. user experience vs. time to develop a 
particular tool is very much still a key consideration to this day.

# The Build

My implementation of a relatively full-featured line editor runs to just shy of 600 lines of C++ code and some portion of that is embedded help text, so it
really is a fairly straightforward tool to develop. It's probably even easier in a language that has a less adversarial relationship to strings and
text processing, but I was in the old-school mindset so C++ is where we ended up. One thing I discovered is that C++ is much less of a headache to use
for text processing if you take the time to define good helper methods.

In particular, it's a shame C++ doesn't define simpler ways of trimming and padding strings but I got on okay with my `trim_left` and `trim_right` methods.

```c++
std::string Editor::trim_left(std::string const &line) {
  size_t start = line.find_first_not_of(" \t");
  if (start == std::string::npos) {
    return "";
  }

  return line.substr(start);
}

std::string Editor::trim_right(std::string const &line) {
  size_t end = line.find_last_not_of(" \t");
  if (end == std::string::npos) {
    return "";
  }

  return line.substr(0, end + 1);
}
```
For the command parsing, I knew every command would begin with `.` but the detailed syntax varied quite a bit, from commands like `.end` to save and quit
that didn't take any parameters, to complex find-and-replace commands that could operate on entire line intervals with basic regex patterns. The full-strength
way to handle this probably would have involved making a mini-parser, but I didn't want to get bogged down in my own set of regular expressions or state
machines, so I took the approach I usually use in Python and handled 80% of the issue by breaking command inputs on known delimiters and building up my
parameter list that way.

Of course, that decision meant I needed a way to split a line in C++, which is shockingly hard because of the Martian logic involved in the design of the
`std::string` substring methods... In the end, I rolled my own.

```c++
std::pair<std::string, std::string>
Editor::split_on_first(std::string const &line, std::string const &patt) {
  size_t idx = 0;
  size_t end = line.find_first_of(patt, idx);
  if (end == std::string::npos) {
    return {line, ""};
  }

  // this range will exclude the split-on character
  std::string first = line.substr(idx, end - idx);

  idx = line.find_first_not_of(patt, end);
  if (idx == std::string::npos) {
    return {first, ""};
  }

  std::string rest = line.substr(idx);

  return {first, rest};
}
```
Beyond the parsing and help text, it's mostly just performing the requested operations and inserting text. Since the whole point was to avoid overly complex
implementation details, I didn't opt for any fancy data structures to enable efficient arbitrary insertion within a text string, I just tossed each line into a
`std::vector<std::string>` and let the STL sort it out. 

The justification is that ultimately, unless you're keeping the text of *Moby Dick* in a single file
and trying to work with it, even a full copy-and-shift to insert at the beginning of the document should be faster than humans can type a line of text. Plus
the typical use case is appending text, where there's no difference between vector append and the fancy stuff.

One thing that did end up being tricky was testing the code. Because it was meant to be part of an interactive system, the most direct thing to do was just fire it
up and see if I could break it by hand, but that wasn't very helpful for regression testing. 

If I was going to release this for real, I'd probably set up a few friend classes to test the methods handling each command, along with general user input 
processing in isolation. If I was really going all out, I'd consider extending the interface to accept a file as input and treat it like a recording of a user's 
session, then compare the results to a hand-checked gold standard file. There are still some issues there, since you can't pick up behaviors that don't get 
recorded in the final save state (like multi-step edits), but combining those approaches plus some manual experimenation would probably be good enough for almost 
all realistic use cases.

# The Build: Part Deux

The other neat feature of this tool-building exercise is that because it was fairly self-contained and I already had an idea of how to approach it, it made the
perfect candidate to learn a bit more Rust. It's one of those situations where I'm always *meaning* to learn the language, but I keep bouncing off of tutorials,
including the community's beginner guide. Having a project I was interested in made it much easier to stay focused.

Interestingly, the Rust code is about as long as the C++ version - it seems like more ergonomic string manipulation gets traded off against the verbosity of
Rust's extra checking on `Result` types. Not that I mind - the extra safety is nice and I think there will be ways to reduce boilerplate once I know more of the
language, but it is interesting that there's not such a difference in how much you're writing. Maybe that makes sense with both being targeted as systems
programming languages, rather than something higher level and intentionally terser?

Either way, I think the biggest things I learned through the process of conversion were first off how to wrap my head around manipulating `Result` values while 
trying to avoid just throwing exceptions, and then toward the end how nice generic lifetimes can be. As far as `Result` goes, I think the language facilities like 
`unwrap_or` go a long way toward letting you move forward with sane results, but it takes more thinking to figure out what the right defaults should be. Lifetimes 
are something I really wish C++ had now that I've seen a little of them - to do the same thing as Rust you're mostly limited to bashing RAII approaches into 
something like the shape you want, but having an explicit way for the compiler to confirm "this thing exists for at least as long as I need it" takes out so 
much tedium and second-guessing. At least C++23 brings `std::expected<T,E>` for something like a `Result` type...

The other big thing I took away is how much Rust supports compile-time validation of good practice, like not missing cases in a `match` statement. C++ can do something
kind of like that with `switch` statements and the right compiler flags, but with Rust you get immediate feedback you've missed something if you're running
a language server alongside your editor. C and C++ usually need something like `gcc -Wall -Wextra -Werror` and `clangd` may or may not flag it depending on your config.
Rust just works out of the box.

# Rolling Your Own Unix Tools

Although the line editor is just a single project, there are lots more examples of the same thing - interesting old tech that still has a lot to teach us
about designing and building useful tools. Some of them I've played with are also drawn from the MUCK world, and I'll probably talk about those eventually,
but one especially rich source of ideas comes from a book called *Software Tools* by Kernighan and Plauger. It's ancient, and my well-loved used copy has 
a Stanford Research Institute decal on the spine so you know the pedigree is top-notch, but what exactly is it? Well, the general premise is "How can I get 
Unix tools if I work somewhere that hasn't heard of Unix?". In the 70s, that was a more reasonable question than today, where Linux kind of ate the internet, 
but the answer remains illuminating if you take the time to unfold it.

In the book, they meet the question by demonstrating how to develop a number of command line tools similar to or inspired by the familiar Unix entries, 
effectively teaching you how to roll your own Unix environment, if not your own OS. While none of the resulting tools are all that helpful by themselves 
in today's world, where the genuine articles are a VM instance away at worst, the real revelation is the first-hand insight the text gives into the logic 
that went into designing these tools originally and the context it builds around why they were needed. The result is what amounts to a host of worked examples 
demonstrating exactly how to weigh the tradeoffs between utility and complexity when building... well, Software Tools.

These little gems are the reason I can't quit exploring ancient software - older code typically deals with more fundamental problems and hasn't had
as much time for incidental complexity to crust over it like a coat of intellectual barnacles. The distilled problem and solution are then almost textbook 
examples, but critically they're ones that solve *real* problems. It's a best of both worlds situation that is hard to find elsewhere.

If you can locate a copy (or a PDF that fell off the back of a truck) of *Software Tools*, I strongly recommend at least skimming it, just to see if it gets
any creative juices flowing.

You should also, of course, support your local MUD/MUSH/MUCK - fantasy RP is a much more palatable way to hone your writing skills than workbooks or 
exercises, and much lower stakes than a presentation to some corporate VP. Plus it's a non-work way to stimulate those juices (creative and occasionally
otherwise...) while keeping things weird and wonderful.
