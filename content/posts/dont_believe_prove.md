+++
title = "Don't Believe Everything You Prove"
date = "2026-08-10"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Briar"
#authorTwitter = "" #do not include @
#cover = ""
tags = ["Math", "Bugs", "Proof", "Formal Proof", "Rant"]
keywords = ["Lean", "Formal Proof", "Collatz"]
description = "All thrones are false."
showFullContent = false
readingTime = false
hideComments = false
+++

In many ways, this is a companion post to my [complaint](../admire_purity) about John Backus' Turing lecture. If you travel
far enough along the smarter-than-thou asshole path, you'll usually find the end of the line somewhere around formal proof methods.

The idea is that if we can elaborate a proof from a well-verified set of operations and a minimal collection of axioms, the thing
we're trying to demonstrate *has* to be correct. It's the ultimate trump card for those seeking a pure, unassailable foundation for
their program (or, increasingly, their philosophy of life).

Or is it?

The problem (for them, at least) is that mathematical proofs are a social construct. Yes, I know this sounds like hippie postmodern
nothing-is-real-maaaaaaaan bullshit, but they actually acknowledge this right up front in the introductions to most formal verification
texts, and even a lot of pure math books too!

My goal is to outline why this is true in general, and then comment on the intersection of this curious fact with the recent news of
a bug in the Lean theorm prover.

# What Is Proof?

First things first, though - what exactly is a proof? Well, as far as mathematics goes, the definition we seem to have settled on is
that a proof is a convincing argument that something is true, typically based on things we already accept as being true.

This definition is vague, of course. What makes an argument convincing? That's really the detail where all the devils in such formal
systems hang out, and the criteria for being "convincing" have changed pretty radically over the years.

Most people credit Euclid with the introduction of the modern axiomatic method in math, where we start from a small set of definitions
or working facts and then deduce whole fields of study as a consquence of these apparently simple origins. All the same, the ancient 
Greeks often accepted geometric or visual proofs growing out of those axioms which later mathetmaticians were skeptical of, particularly 
as analytic geometry and algebra flourished, meaning most people were much more familiar with numeric manipulations and their enhanced precision
compared to visual demonstrations that might subtly rely on aspects of scale or propotion.

Despite this change, the overall complexity of proofs remained fairly stable down the years, more or less up through the advent of calculus 
and its early development. Most proofs were understood in terms of relatively familiar (and well-behaved) examples, and a good deal of
what made the demonstrations work was still left up to interpretation and intuition.

Somewhere in the 19th century, that began to change drastically. As people pushed into more and more esoteric applications of calculus, formalizing
the discipline of real analysis, traditional proofs started to reveal some problematic gaps. Widely-assumed facts found counterexamples -
fuctions that were everywhere continuous but nowhere differentiable, or those that were everywhere discontinuous but still integrable! Mathematics
suddenly had to grapple with a need for much more precise specification of the conditions where proved theorems held true, leading to a re-analysis of
entire subfields of study.

And then the wheels really came off.

Bertrand Russel and Alfred Whitehead, while trying to formalize math's foundations, managed to find paradoxes which forced a restructuring
of set theory from the axioms up. Not much later, Turing showed that the solutions to certain equations could exist without being computable, 
and Gödel demonstrated the almost unbelievable fact that there existed true theorems in mathematics which could not be proved from the axioms of the system.

Perhaps ironically, these major upsets came at a time when pure mathematics was trying to solidify its claim to absolute certainty and rationality. 
Rather than cementing a perfectly consistent and unarguable edifice of pure reason, the attempts to formalize math's foundations exposed a whole new 
stratum of fascinating and difficult problems!

In some ways, I'd argue that's a much better result. It's certainly a much more *interesting* one.

# Enter the Machine

The other wrinkle with proofs has to do with computers, specifically with the idea of machine proof and formal verification. The idea is
fairly simple, though not uncontroversial. 

If we have a small core of proof tactics that are well-verified, we can use these to manipulate
constructs built up from a well-specified computer language and arrive at conclusions with no possibility of failure due to subjectivity -
no misunderstood premises, no elided demonstrations, no "clearly", "elementary", or "obviously" used to batter down objections.

Just cold, hard, mechanical logic.

Of course, there's disagreement here too. Mostly around all those "well-verified" and "well-specified" weasel words. Most people who interact
with a computer for any length of time are familiar with bugs, and it's easy to see how the crisp perfection of a mechanical mathematician
could be spoiled by some silly logic error in its proof engine.

Typically, the response is to focus on minimizing the moving parts in the proof methods and the language itself, paired with rigorous specification
and torture testing of each and every component.

With the advent of a computer proof of the Four Color Theorem, computerized proof systems became more and more accepted as an aid to (or outright
source of) new proofs.

While that might seem to take the wind out of the "proof as a social construct" view, at least where formal methods are applicable, it turns
out that bugs in proof systems aren't so hypothetical. Even the extremely well-tested ones.

# So, Funny Story...

About those bugs - Lean, probably the most famous pure-math-oriented prover recently fell victim to an embarrassing one. A researcher was able
to submit a complete proof of the [Collatz conjecture](https://en.wikipedia.org/wiki/Collatz_conjecture) which passed all checks. If it had
been genuine, it would have been a huge result (at least for folks interested in these sorts of long-standing unsolved problems), but it
turned out that the only reason verification succeeded was due to a bug in one of the darker corners of Lean's type system.

Based on the discussion by Lean contributors on Mastodon (Mathstodon?), it looks like this flaw in the engine came about because that portion
of the type system was complex and ambiguous enough that it hadn't been thoroughly explored elsewhere, meaning Lean's implementation was the
*de facto* reference implementation.

As a consequence, the other tools that were designed to cross-check Lean's results more or less ended up copying its homework, and were
unable to unearth the bug independently.

Since the person who submitted the proof has been involved pretty deeply with the formal verification community, there's also speculation 
that this might have been an intentional attempt to shine light on a particular shortcoming of the system.

Either way, I think it underscores the fact that the pesky human element will remain with mathematics for a long time, and that proofs will
continue to depend on the notion of what we as humans find convincing.

# Limits of Knowledge

All this of course doesn't mean that we can't know things, or that mathematical proofs are useless. But it does remind us not to put blind
faith in purity - pure mathematics, pure reason, pure intellect. And those who would wield such things as a cudgel would do well to remember
the shock of the Incompleteness Theorem, or our little bug of the day today.

We must become comfortable with at least some sliver of ambiguity, of uncertainty. And we must always relate what we think we know back to
the real world and the real experiences of ourselves and others.

If we're very lucky, we might prove ourselves wrong and find there are even more worlds to explore than we thought!
