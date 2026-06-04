+++
title = "LLMs Are Bad at Software"
date = "2026-06-01T22:27:41-07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Briar"
#authorTwitter = "" #do not include @
#cover = ""
tags = ["Rant", "AI", "LLMs"]
keywords = ["LLMs", "Software", "Engineering"]
description = "And they'll make you worse at it too."
showFullContent = false
readingTime = false
hideComments = false
+++

I am incredibly tired of "reasonable" takes about LLMs being used to develop software. "Well, they're useful for-" No! They're
unethical plagiarism machines that exploit some of our worst addictive impulses while being trained to flatter us so we'll
spend more time and money on an ecologically destructive and inefficient method of approximating text sequences. They are
insidious and I'm going to lay out how they screw up your coding and your critical thinking in all sorts of ways.

The "AI is fine in moderation" takes hit me at this point in the same way as those pseudo-intellectual claims to "centrism" which wind up defending
fascists and Nazis because both sides have some good points which ought to be heard... And make no mistake, the people with monopoly control
of these fabrication engines are absolutely on the side of the fascists right now, handing over VC money to the worst politicians 
you know because deep down they want the same absolute control the Nazi fucks are trying to grab for themselves. 

They're gleeful over the idea that maybe if they click their heels together enough times they'll be able to claim exclusivity
over both labor and production while regular people starve.

Fuck them and fuck the people who support them.

**\*Ahem\***

With that out of the way, let's look in a little more detail at the ways that using LLM "tools" rapidly and profoundly undermines
your ability to develop software while atrophying your analytical capacity.

# Subtle Bugs Are the Worst

A point which seemed obvious to me from the very beginning of the LLM coding push is that agents being able to create a reasonable
approximation of functional code is actually kind of a nightmare for correctness. If you've spent any time working on a significant 
piece of code, you've probably come to realize that the severity of bugs goes something like:

`Doesn't Compile` -> `Segfault/Obvious Crash` -> `Consistent Logical Bugs` -> `Race Conditions and Subtle Logic Bugs`

If an LLM can output code that's at least syntactically valid and is made to iterate on the code until it no longer crashes, you're 
left with only the most severe bug categories to deal with. And the best way to rule out those issues is to have a very clear sense
of what code has actually been produced and how it relates to the problem domain you're working in (this is basically the informal
version of Floyd-Hoare proofs). 

But that connection to the code and the details of the implementation is precisely what you give up when
you leave code generation to a stochastic process - even if you've followed all the guidelines and tried to
build up LLM code from a clear problem statement, multiple model examples, and a well-defined spec, you still don't really
know anything about the implementation until you read it, and the implementation is where all those worst-of-the-worst bugs lurk...

But surely the tests will catch it, right?

# Your Tests Suck

Look me in the eye and tell me your testing story doesn't suck. Maybe, maaaaybe for a few smaller teams working with high autonomy
in an organization that prioritizes technical skill above all else you can raise the bar to decent testing. But most testing sucks.
Most of it looks something like this:

- Incomplete coverage 

- Broken assumptions so tests always pass 
- Flaky test results because half the org doesn't know what a race condition
even is
- Fixes that amount to special casing either the code or the test
- Untestable implementations of major features

On and on and on and on...

Odds are good you've seen most or all of these if you've worked professionally for more than a year or two. Odds are good you've contributed to
some of those issues yourself. Do you really trust your current test suite to catch subtle logical corner cases in new code? Or
to suss out intermittent race conditions? Catch memory leaks? Silent buffer overflows?

Will you even be able to tell that a failing test means something is broken? Or is it just flaky?

The average corporate test suite is not an environment where you can leave junior developers unsupervised, let alone LLMs. If you 
don't believe me, go read up on how many fresh incidents have been caused at any of the Big Tech names after their AI-first mandates 
rolled out. Things are breaking in large part because everyone's testing sucks. And the only answer the boosters have is to try and 
write more tests using AI. Is the chicken-and-egg problem apparent yet?

Probably the only way to responsibly ship LLM-generated code is with careful, exhaustive human review to ensure the apparent
semantics match up with the intended ones.

# Reviewer Fatigue on Steroids

Of course, human review doesn't scale. Again, if you don't believe me, just go look at the spate of articles we've had about
senior engineers and open source maintainers burning out faster than ever because of the burden of wading through mountains
of AI slop.

Reviewing is no one's favorite activity, so it tends to get relegated to a half-awake `LGTM` first thing in the morning or
a few vanity nitpicks to assuage the guilt of lingering PRs before an engineer packs up for their three day weekend. Making more
of the job about reviewing code is simply not going to end well. Human nature and laziness will win out, and code that merely looks right
will get shipped (arguably it's already getting shipped, based on the reports of severe incidents due to AI changes at Amazon and
friends). But code that looks right doesn't always behave right, and the more complex the application domain, the worse that mismatch
can be.

Oddly, I keep seeing the AI stans try to claim that forcing yet more tedious review of LLM code onto overworked humans is somehow shifting the job
back toward "big-picture engineering" and away from "tedious coding and implementation". 

I don't know about them, but I actually *like* 
writing code and solving problems; certainly I like it much more than parsing out what a plagiarism machine fattened off mediocre student GitHub 
projects was doing by stringing together some unholy sequence of function calls. Making development into LLM review drudgery isn't "liberating", 
though it does make it feel more like a middle manager's job, which seems to be all the suits really understand anyway.

# Bottleneck Detected Between Keyboard and Chair

I intetionally held off from discussing what LLM advocates always offer up as the panacea to this exponential scaling of slop review -
in their eyes we just have to scale up even faster by letting agents handle more of the review process! Simply create new code review agents to validate
the code your existing coding agents spit up. Simple! Genius!

I wish I could poke holes in their confidence by pointing out that we don't let *humans* review their own code for a host of good reasons, 
but after watching multiple otherwise-sane engineers fall victim to this kind of thinking, I suspect it stems from a deeper psychic wound.

Personally, I think the contagion ultimately arises from the fact that LLMs are marketed as being so much more *productive* than a human developer,
with the strong implication that this productivity leads to them generating more *value* than a human developer. They repeat this even though LLM 
productivity is mainly measured by lines of code (or simple proxies thereof), a value metric we've known was bogus for decades. If productivity-as-quantity
becomes your guiding metric, you very rapidly slam into the realization that you can't churn out code nearly as fast as you want to because of
all the non-coding bottlenecks in the system.

In fact, every human-in-the-loop system has one huge, glaring bottleneck: the human. 

Since you've already ceded ground to letting the AI write code for you, it becomes that much easier to talk yourself into letting it 
review the code too - after all, it's probably a separate instance, or you can use a different model, or different context, or a different 
agent... And then why bother spending all this time running a full regression test suite on every check-in? You could probably have some 
automated agent select which tests are most informative based on the PR diffs and cut way down on time-to-merge. Then of course 
you need some LLM automation to deploy to production, plus AI monitors, and an agentic way to roll back bad code or roll out hotfixes...

You might think I'm joking, but I have heard every single one of these proposals discussed with stone-faced
seriouness by people with Principal or VP in their titles. Often the whole list has been proposed in rapid succession within the same meeting.

And don't get me started on the plans to have an LLM document the code with minimal oversight. "Dictated but not read", indeed.

# Friction is Good, Actually

Mistaking quantity for quality is only one of the issues at play here, though. The underlying impulse toward making all aspects of 
development automated and frictionless fails to appreciate that sticking points are often an indication that we're heading for a dead end. 

Maybe we're solving the wrong problem, or maybe our approach is too inefficient and can't scale the way we need it to. Whatever the case, those
moments writing code where you think "Oh, this will be a pain" or "Yikes, this takes so long to run, even on the test cases" are the first 
warning signs that something important is getting overlooked.

LLMs experience no such doubt, and will happily volunteer to write code for finding arbitrary Busy Beaver numbers, but *you'll* be the one stuck 
wondering why your program never finishes. The disconnect is magnified for real problems where exponentially complex gotchas can lurk in the 
cracks of seemingly innocuous specifications.

More generally, I'm compelled to argue that in fact, there is no clear separation to be made between design, specification, and coding
in software engineering. You can't draw an arbitrary line and say that everything to one side is just mechanical implementation of some nobler idea.
Each phase informs and updates the others, making software engineering much more like solution by successive approximations than an *ab initio* 
laying down of some immutable axiomatic proof. 

Indeed, I often find that aspects of a problem I never even considered during higher-level design suddenly smack me in the face while I'm actually 
writing the code. And they tend to do the smacking with all the tooth-rattling force of a missed step off the curb. It invariably means going back 
to the drawing board to address the underlying issue, but avoiding that effort would only leave the code broken or badly unfit for its purpose.

Iterative refinement end-to-end has long been a guiding principle for software engineering, but there are lots of developers now ignoring the related 
feedback loop between design and implementation. If we delegate writing code to an LLM, we lose most of those feedback points, especially if we're
eager to take it at face value when the mechanical sychophant tells us "Job's done, boss".

This is an arena where even perfect testing and the strictest compiler settings can't help guide AI, because often what's lacking is an entire
branch of the program specification itself. Overlooked connections, unrecognized assumptions, or just the ever-present drift in user
needs over the course of a project - developing high-quality, useful software means taking advantage of any and all opportunities to close those
gaps, and I'm convinced that means deeply involving yourself in the details of code construction.

To give a parallel example that might be familiar, think back to math and science courses in school. I'm willing to bet that you've had
at least a few experiences where you looked at something on the homework, went "I know how to solve that", and then wound up in a hopeless muddle
halfway through because you only clearly saw the start and end of the process. The connection you assumed would come naturally wound up 
missing, and the absence only became clear when you started the seemingly rote process of stitching together a proof from your rough outline.

Should we really expect complex problems arising from real world needs to be any less challenging to address than textbook examples? So
why do we give the proof of Euclid's algorithm more care and attention than the development of a distributed database?

# Software as Theory, Software as Process

The science homework example may be closer to the truth than it seems. Peter Naur famously argued that programming was theory-building, a reflection
of our understanding of the world. That understanding improves only when we actively engage with both the theory we're constructing
and the world it represents. Delegating the details to someone else won't get us there.

Viewing software as theory also means dispensing with the idea that code itself is the primary end goal of development. It's certainly the most
concrete artifact, and the thing that ultimately gets shipped, but it's a snapshot in time of the process that created it. Meanwhile, the
context involved in that creation, and much of the world-modelling it represents still lives in developer's heads. Even a broad view of code that
includes configuration, documentation, and design specifications fails to account for the intrinsic human factor of the theory building, but
it's precisely this aspect which becomes most important during maintenance or updates - any time the software needs to evolve, that understanding
is required to make the changes work in a sustainable way.

Arguably, legacy code is code that works but which no longer has anyone available to explain why the decision was made for it work this way originally.
In turn, that makes all LLM code legacy right from the outset. The process of development has been short circuited and an essential aspect
is missing with no way to recover it - we have the how but not the why. Usually, when writing comments we deplore this sort of mechanistic rehashing,
so why should we tolerate it for entire programs?

# Reasonable Conclusions from an Unreasonable Rant

To be blunt, the problems outlined above (along with many more like them) mean that LLMs should not be used for software development. While they may be able 
to generate functional code in some cases, it's not without serious costs - engineers *must* spend time validating the outputs line-by-line or else they'll 
spend it fixing what goes wrong when unvalidated LLM code escapes into the wild. 

More recently, even the cost in cold, hard lucre is becoming impossible to deny as more and more model providers switch over to direct API billing in place 
of subscription fees. Executives who once practically commanded their employees to burn tokens are now taking a step back and asking for moderation and 
efficiency in deploying agentic systems. It is rapidly becoming clear that not only will using LLMs make you a worse engineer, they'll charge an arm and 
a leg for the privilege.

Just don't use them.
