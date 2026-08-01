+++
title = "Please Do Not Admire Its Purity"
date = "2026-07-31T23:52:26-07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Briar"
#authorTwitter = "" #do not include @
#cover = ""
tags = ["Rant", "Functional Programming", "Jerks"]
keywords = ["Functional Programming", "Turing Lecture", "Backus"]
description = "Picking fights with dead men."
showFullContent = false
readingTime = false
hideComments = false
+++

The title, if you didn't catch it, is a reference to the movie *Alien*. 

Specifically, it's a reference to the part (spoiler alert for a movie from 1979) where the crew discovers one of 
their fellow crewmates, Ash, is actually an android. They discover this when he tries to kill Ellen Ripley, 
our protagonist, and after he's clubbed to death with a fire extinguisher, the crew reconnects his severed head 
for a final interrogation.

In the course of that interrogation, they discover the whole mission since their ill-fated pit stop on the way home has been
a setup by the Weyland-Yutani corporation, part of a covert effort to recover an alien bioweapon, and in the balance of power and profit,
they've all been deemed expendable.

The part that sticks with me though, is a quote from near the end of the scene, when Ash makes it clear he's been
rooting for the alien:

> Lambert: "You **admire** it."
>
> Ash: "I admire its purity."

It's creepy - a one-liner that underscores just how inhuman Ash's perspective is, despite his appearance and prior behavior.

Unfortunately, not everyone seems to agree with my take.

# The Search for Purity

I've been reading a lot on functional programming and functional languages recently. In part this has been an effort to give its staunchest
proponents a chance to make their case before I rant about perceived shortcomings (except now I'm writing this... oops),
and in part because there's a lot there I genuinely do like.

The problem is that most functional purists are also raging assholes.

If you think I'm exaggerating, please consider some actual quotes:

> Developing a useful, general framework for expressing relations among different types of entities [...] seems
intractably difficult. The main difference between the confusion that existed ten years ago and the confusion that
exists now is that now a variety of inadequate ontological theories have been embodied in a plethora of of
correspondingly inadequate programming languages.
>
> --- *Structure and Interpretation of Computer Programs*, 2<sup>nd</sup> Ed. p. 200, footnote #52.

This is how LISP-ers dump on C++. But glass houses and stones, and all that...

> Most ML dialects include imperative features, but ML is more disciplined than Lisp.
>
> --- *ML for the Working Programmer*, 2<sup>nd</sup> Ed. p. 9.

That's an ML-stan bagging on LISP for being *too imperative* - in the functional world, "imperative" is pronounced like a slur.
But wait, the gloves aren't really off yet!

> If you prefer this version of *prod*, you might as well give up ML for Lisp. For added clarity,
> Lisp primitives have names like CAR and CDR. Normal people find pattern-matching more readable
> than *hd* and *tl*.
>
> --- *ibid.* p. 75.

Preferring LISP makes one an abnormal psychology case! So the functional bros are perfectly willing to eat their own, provided
it's a type of FP they themselves don't approve of.

The theme of these quotes is of course, that the language in question doesn't conform to the speakers' idea of what
makes a programming language good - and that usually means pure, functional, and ideally as close to mathematics as possible.

# So Who's This Asshole?

One more quote, an example, and I'll be done, I promise. This one's the real big fish, though. John Backus, in his 1977
Turing Award lecture, used the opportunity to take shots at what he labeled as the "von Neumann Style":

> Conventional programming languages are growing ever more enormous, but not stronger. Inherent defects at the most basic
> level cause them to be both fat and weak: their primitive word-at-a-time style of programming inherited from their common
> ancestor--the von Neumann computer [,...]
>
> --- CACM, Vol. 21, Number 8, August 1978, p. 613.
>

It's pretty much all like that, yeah. Backus does attempt a pass at modesty later in the paper by explaining he isn't criticizing
the great mathematician John von Neumann, just the supposedly-terrible effects on programming that a close coupling to the architecture
which bears his name has spawned.

>"Cure is simple - read two papers of Great Mathematician Johnny von Neumann." 
>
>"But doctor-" 

Sorry. I said just one more quote.

On the one hand, the Turing Award winner might have a point - functional languages offer us some really nice ways of combining
subcomponents. There's a reason `map`, `filter`, `reduce`, and `accumulate` are so well-known, even outside of diehard functional
circles.

On the other hand, Backus tries to convince us that **this** is a straightforward and superior way of expressing matrix multiplication:

```
Def IP ≡ (/+)∘(𝛼⨯)∘trans
Def MM ≡ (𝛼𝛼IP)∘(𝛼distl)∘distr∘[1,trans∘2]
```

That `MM` up there - that's matrix multiplication in his point-free combinator style. Good luck deciding if that's actually right or
not. I very well may have mis-copied it.

Even if we get comfortable with the language-level meanings for the symbols he's proposing, the actual definition of matrix multiplication
in effectively any domain that cares about numerical computation is:

>C<sub>ij</sub> = **A**<sub>i</sub>∙**B**<sub>j</sub>

In other words, each entry in the result matrix `C` is the dot product of a row of `A` with a column of `B`. In coordinate form, that
becomes simply:

>C<sub>ij</sub> = ∑<sub>k</sub> A<sub>ik</sub>B<sub>kj</sub>

This expression in turn maps almost trivially to the procedural code:

```C
for (int i = 0; i < N; i++) {
    for (int j = 0; j < M; j++) {
        // the mathematical core from above
        for (int k = 0; k < K; k++) {
            c[i][j] = a[i][k]*b[k][j];
        }
    }
}
```

The outer loops make it slightly more verbose, but in the math, iteration over all indices in the result matrix is implied - at least if
you actually want to write down the result somewhere.

And there's where I think Backus' argument falls down. His complaint about word-at-a-time (read: procedural) languages is that they involve
lots of state that isn't obvious just from the code itself, and which therefore must be carried around in the programmer's brain. While that's true
in the worst cases, in many others the "extra" state is a natural component of the problem itself, usually operations we would perform explicitly
when executing the algorithm by hand.

Is it even obvious *how* to execute Backus' algorithm for matrix multiplication by hand? 

I know which one I prefer, at least.

Going further, I'd argue a much better take on writing programs comes from another CS luminary - Donald Knuth. In *The Art of Computer Programming* he
claims that no one really understands an algorithm until they've stepped through it a few times by hand on a small example. 

I believe Knuth is right, because his approach is the only way I've ever come to understand algorithms, and in fact, taking a math concept and turning it into
an algorithm in this way is often how I convince myself I really understand whatever idea I'm currently trying to come to grips with. Running the algorithm
allows me to test my understanding in real time and find counterexamples.

The thing is, executing an algorithm by hand *a la* Knuth involves a lot of scratch space, a lot of messy state to track all the intermediate results and remind
us where we are in the process (think long division of decimal fractions). Computers may not need all of that, but why bend familiar equations so badly out of shape just to satisfy an
arbitrary requirement of expressing them in a "pure" combinator style?

# About That Bottleneck...

Another problem with Backus' argument also rests on pragmatic grounds. He criticizes the bookkeeping a von Neumann architecture requires to map data to 
storage locations. And it's true the von Neumann bottleneck between memory and CPU *is* a real bottleneck (ask a cache designer if you don't believe
me), but we seem to be stuck with it.

The problem, ultimately, is that we can devote silicon to logic gates or to storing bits, but we don't have a good
way of doing both. It's a fairly natural follow-up to that limitation to then split out storage from execution, specialize each separately, 
and modularize them - indeed, combining well-defined submodules to achieve more complex behaviors is a key theme of Backus' own paper!

The catch is that splitting responsibilities between modules requires the modules to then talk to one another, and so state enters the picture in the form of 
memory addresses and operations devoted to loading and storing from those addresses. 

From the physical and engineering standpoint, it's not clear how we might go about resolving this split at anything like a fundamental level,
somehow melding memory and CPU functions into a single whole. Most likely we would need a sea change in computer architecture at the very least, from the
raw silicon on up.

While we have increasingly embraced parallel data transfer in the face of tightening von Neumann bottlnecks, it's not
clear this solves the underlying complaint of Backus' paper - even the widest CPU vector registers deal with only a fraction 
of a percent of available RAM, and their use is hardly free from messy implementation details and state juggling.

It could be argued that Backus doesn't have a problem with the hardware per se, but rather the extent to which it's maintained an intellectual grip
on what should be higher-level interfaces to that hardware. On that count, he certainly scores points - modern languages have embraced functional
ideas alongside procedural and object-oriented ones, and even the perennial whipping boys (like C++) now offer surprisingly expressive constructs in
the base language.

But even with the goalposts shifted, it's hard to call it a complete victory...

# Who Needs Fast Matrix Multiplication?

We come back to the same example I referenced earlier - matrix multiplication. Even if we decided Backus' form of the equations is superior on
theoretical grounds, they're certainly not optimal from a performance standpoint (especially when you consider that hiding word addressing typically
hauls in a garbage-collected runtime alongside it).

This isn't an arbitrary complaint - fast matrix multiplication algorithms are the bedrock of practically all scientific and engineering code, and
they also form the basis of every rendering engine out there. Just accounting for the gaming market, that latter application is worth billions today.

If we expand to consider numerical computing more broadly, we run into video and audio codecs, avionics systems, and just about every software
application that *isn't* pure SaaS. 

That market is so large, I'm not actually sure we can quantify exactly how much numerical algorithms contribute to common, popular applications.

Unfortunately, efficient numerical computation is hardware-specific and requires deep consideration of the exact sort of word-at-a-time behavior that
Backus criticizes. The functional nirvana heralded by so many language purists would cripple entire markets if seen through to its logical conclusion!

# Beware Purity

So why do I bring this up? I think the quest for "purity" - often in the guise of logical consistency or aesthetic perfection - is equal parts 
natural and dangerous. It's a totalizing impulse, and those rarely remain confined to the purely conceptual realms of programming or mathematics.

Worse, when they do make contact with the real world, such impulses are often hideously destructive. 

As a \*completely\* hypothetical example, consider the mingling of techno-puritanism with the loose outlines of libertarian economic thought 
(which has its own powerful tendency to encourage extremist conceptions of the world). An environment like that would be the perfect toxic 
breeding ground for a type of techno-feudalism that would turn all automation into tools of oppression to be wielded against everyone outside the C-suite...

Good thing there's nowhere like that in the real world, huh?

But genuinely - it's important to grapple with the limitations of our thinking and of our systems. 

In practice, getting work done and existing in the world requires a series of compromises, and we must remain open to changing our perspective 
and our behavior as we learn more and make contact with real people and real situations that may not map to our preconceived ideas of how 
things "ought" to be.

In short, please don't let math turn you into a Nazi.
