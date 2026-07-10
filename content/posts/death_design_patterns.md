+++
title = "The Death of Design Patterns"
date = "2026-07-04T23:46:51-07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Briar"
#authorTwitter = "" #do not include @
#cover = ""
tags = ["Rant", "OOP", "C++"]
keywords = ["Design Patterns", "OOP", "Architecture"]
description = "My hottest take."
showFullContent = false
readingTime = false
hideComments = false
+++

Every now and then, I see *Design Patterns* floated as a kind of next-step resource for
programmers after they've gotten comfortable with a language, typically an object-oriented
(or at least object-aware) one. I don't particularly have a problem with OOP as a concept,
though I agree with its critics that inheritance is overused in teaching materials by several
orders of magnitude.

But as for *Design Patterns* itself, I personally think these days that it only serves as a window into
90s programming philosophy - a historical relic of a bygone age, and one that I'm not all
that interested in revisiting (unlike my passion for *Software Tools* from the 70s).

So what gives?

# It's Just Abstraction

The first big failure of the book for me is wallowing in a certain kind of middle-aged 90s
consultant guy vibe right off the bat by calling these things "Design Patterns" at all. In
the intro they go into how the name is drawn from a concept in architecture, and there's nothing
tedious 90s consultant dudes love more than droning on about architecture as a mark of sophistication.

I don't know why it's architecture specifically, but I've seen it more than once and once is
already more than enough.

The thing is, we already had a name for "common, useful abstractions". We called them "useful
abstractions" because that's what they are!

Giving these things some kind of separate name and requiring you to decipher UML diagrams to
work with them makes it seem like this is somehow separate from (and conveniently for the consultants,
more advanced than) just recognizing commonalities and factoring them out or learning from
well-known solutions popularized by others. Now you have to ask whether or not something is
a capital-P Pattern, and if so is it in The Book, and if not do you have to make the diagrams yourself
for it to qualify for the club, or what?

Not to mention that the concept of abstraction is fundamentally broader - functional programming doesn't do 
\~Design Patterns\~ but it understands abstraction, and using a common language across paradigms helps you identify 
other useful instances to learn from and incorporate in your own work. Making up artifical categories on a
whim actively impedes communication and the learning that grows out of it.

Alas, my own personal linguistic pet peeves are not where the problems end.

# Design Antipatterns

From a technical standpoint, the biggest body blow against the book is probably that some of the 
patterns identified are actively bad, and others are painfully overused by people eager to do things
rigidly by the book.

The worst culprit in the outright antipattern category is probably Singleton, which is just a way to carry over
global variables from procedural languages like C into pure-OOP ones like Java. Especially in the
modern era of enforced concurrency, global variables are the root of much suffering. 

Why exactly does the expert's book on good design include this misbegotten creature?

The runner-up is probably Flyweight - I genuinely do not understand how this is supposed to work. In
principle, it enables you to be object-oriented all the way down to the most fine-grained level by not
copying around and re-creating the lowest layers of these micro-objects and instead just passing 
references to them, but on real machines you rapidly run into the problem that references to the Flyweights outweigh 
the resources required to copy around the underlying data anyway! 

The example in the book talks about representing every character in a text editor as objects via this pattern, 
but especially on modern hardware, even a raw pointer to the Flyweight object will consume 8 bytes and will 
cost more space than just copying around UTF-8 characters directly. And that's before you get into the 
overhead of making both the references and the referencee (that's probably not a word...) into full-blown 
objects with the storage and lifetime overhead involved.

By far the largest group of patterns guilty of setting bad examples is one that I'd consider *conditional* antipatterns, and these are mostly what I 
think of as the library designer suite in the book. These are patterns like Factory and Builder which are presented as solutions to 
everyday problems, when their real use is decoupling platform-specific implementations from the caller interface. 
There are cases where this comes up in regular application programming, but with a sane set of libraries, that complexity is properly
encapsulated by the library itself - think of using SDL to make a game that runs on Linux or Windows without
having to maintain two completely separate stacks of manual window manager code.

True, if you're the one writing the SDL source code, those design patterns might be helpful, but only a very small minority
of developers work directly on library design or implementation. Treating narrow-but-useful solutions for one specific
audience as bread-and-butter abstractions is how you get Java monstrosities like `AbstractTestPreconditionConfigurationFactoryManagerFactory`.

# Too Much of a Good Thing

The over-abstraction encouraged by this kind of wannabe-library approach isn't limited to Java, either. I've seen real examples where
practically every piece of a system is required to be accessed through an abstract interface, just in case it needs to change later. The
net result of preparing for eight varieties of every object in your system when 90% of the time you only ever implement one is that you
waste developer time and system resources on traversing layer after layer of "abstraction".

Worse, these abstractions are often leaky and you need to know something about the object or method you're really using in order to
reason about whether or not it's appropriate for the current situation, which completely eliminates the usefulness of an abstract 
interface in the first place!

Even in the case where you are legitimately adding to the initial implementation with a parallel one, it's easy to get lost in the 
18-layer wedding cake of boilerplate to try and find where and how the actual object used in the program is being selected and instantiated...

If premature optimization is the root of all evil in computer science, I think premature abstraction is probably at least a branch on the tree.

# Beg, Borrow, Steal

Other patterns might not be so completely through the looking glass as to qualify for antipattern status per se, but they amount 
to little more than attempts to smuggle in concepts from other language paradigms. This isn't necessarily bad, but it's a response to the constraints
of a particular approach to programming rather than some universal principle, so enshrining it as a Big Important Idea seems disingenuous.

The exemplar here is Command - if you don't have first-class functions, wrapping them as methods on an object you **can** pass around 
freely does give you some of the same execution flexibility that more function-forward languages enjoy. The caveat is that much of the supporting
infrastructure that makes passing the functions themselves around useful in those other languages is still lacking. In other words, *you'll* 
still be the one implementing your own custom OOP version of `map` and `reduce`.

This is one of the big reasons to use a multiparadigm language in my opinion - sometimes objects are a good fit, other times you just want a free function.
Purity can be useful as a way to push an idea to its limits to see what can be learned, but dogmatism in what amounts to tools for solving
problems will only limit your choices and force you to contort yourself in ugly ways when you run up against something that doesn't fit neatly
into your crystalline vision of the world. I'd argue the state of most modern programming languages supports my point - far and
away all the major languages used in solving real problems are now multiparadigm, or at least "impure" by PL nerd standards: Javascript, Python, C++, 
Rust, etc.

# Victims of Their Own Success

While some concepts genuinely might have been popularized via their inclusion in *Design Patterns*, the most successful ones were so influential
that they've transcended the book completely. The obvious example is probably Iterator - genuinely a useful abstraction within OOP
as a way to efficiently traverse opaque collections of objects.

But the core idea of generalizing array indices or pointer arithmetic doesn't really *require* OOP-style objects to make sense, and the inclusion
of this concept in pretty much every major language in modern use speaks to its generality and utility. You're just as likely to learn about the
pattern from reading through your language's standard library (especially if you work in C++) than by looking through the pages of *Design Patterns*.

And even in the case of a few standout successes like Iterator, we see the library-centric pitfalls of the book still lurking in our peripheral vision. After all, 
relatively few developers actually need to create a full-on iterator implementation for their projects, they just need to use the libraries that have
the concept already baked in. Even in the marginal cases where types or classes in the application itself need to be extended with iterator support,
this typically amounts to implementing an interface, rather than designing a full-strength abstract hierarchy of iterators from the ground up.

# What Should We Do?

In my view, all these strikes against *Design Patterns* means we should stop using their "design pattern" term to describe programming constructs,
and instead fall back to the more honest description "useful abstraction" provides. That way, we don't have to get stuck debating if the thing 
we're working with is *really* a capital-P Pattern or not, or what we need to do to make it qualify as one. We can also easily clarify our designation 
by specifying where and when the abstraction is useful, instead of assuming it must be some kind of totally general catchall across all development 
everywhere (even though we're really just looking at, say, hyper-general library development).

We should also stop recommending the book to new programmers. This far removed from the OOP fever of the mid-90s, it's just going to confuse them
and compete with the many other (more important) things they need to pick up as they start writing real code to solve real problems.
