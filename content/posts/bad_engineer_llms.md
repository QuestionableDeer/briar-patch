+++
title = "LLMs Are Bad at Software"
date = "2026-06-01T22:27:41-07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Briar"
#authorTwitter = "" #do not include @
#cover = ""
tags = ["Rant", "AI", "LLMs"]
keywords = ["LLMs", "Software", "Engineering"]
description = "And they'll make you worse."
showFullContent = false
readingTime = false
hideComments = false
+++

I am incredibly tired of "reasonable" takes about LLMs being used to develop software. "Well, they're useful for-" No! They're
unethical plagiarism machines that exploit some of our worst addictive impulses while being trained to flatter us so we'll
spend more time and money on an ecologically destructive and inefficient method of approximating text sequences. They are
insidious and I'm going to lay out how they screw up your coding and your critical thinking in all sorts of ways.

The "AI is fine in moderation" takes hit me at this point like pseudo-intellectual claims of "centrism" that wind up defending
fascists and Nazis because of course both sides have some good points... And make no mistake, the people with monopoly control
of these fabrication engines are absolutely on the side of the fascists right now. They're gleeful over the idea that maybe
if they click their heels together enough times they'll be able to claim exclusive control over both labor and production while regular
people starve.

Fuck them and fuck the people who support them.

\*Ahem\*

With that out of the way, let's look in a little more detail at the ways that using LLM "tools" rapidly and profoundly undermines
your ability to develop software while atrophying your analytical ability.

# Subtle Bugs Are the Worst

A point which seemed obvious to me at the very beginning of the LLM coding push is that agents being able to create a reasonable
approximation of functional code is actually kind of awful. If you've spent any time working on a significant piece of code, you've
probably come to realize that the severity of bugs goes something like:

Doesn't Compile < Segfault/Obvious Crash < Consistent Logical Bugs <<< Race Conditions and Subtle Logic Bugs

If an LLM can output code that's at least syntactically valid and is made to iterate on the code until it no longer crashes, you're only 
left with the most severe categories of issues that remain. And the best way to rule out those issues is to be very clear on what code has
actually been produced and how it relates to the problem domain you're working in. But that's precisely what you give up when
you leave the code generation to a stochastic process - even if you've followed the best guidelines we have and have tried to
build up LLM code from a clear problem statement, multiple similar examples, and a well-defined spec, you still don't really
know anything about the implementation until you read it, and the implementation is where those worst-of-the-worst bugs lurk...

But surely the tests will catch it, right?

# Your Tests Suck

Look me in the eye and tell me your testing story doesn't suck. Maybe, maaaaybe for a few smaller teams working with high autonomy
in an organization that prioritizes technical skill above all else you can squeak by with decent testing. But most testing sucks.

Incomplete coverage. Broken assumptions so tests always pass. Flaky test results because half the org doesn't know what a race condition
even is. "Fixes" that amount to special casing either the code or the test. Untestable implementations of major features. On and
on and on and on...

Odds are good you've seen it if you've worked professionally for more than a year or two. Odds are good you've contributed to
some of those issues yourself. Do you really trust your current test suite to catch subtle logical corner cases in new code? Or
to suss out intermittent race conditions? Catch memory leaks? Silent buffer overflows?

Will you even be able to tell that a failing test means something is broken? Or is it just flaky?

The average corporate test suite is not an environment where you can leave junior developers unsupervised, let alone LLMs. If you 
don't believe me, go read up on how many fresh incidents have been caused at any of the Big Tech names after their AI-first mandates 
rolled out. Things are breaking in part because everyone's testing sucks. And the only answer the management suits have is to try and 
write more tests using AI. Is the chicken-and-egg problem apparent yet?

Probably the only way to responsibly ship LLM-generated code is with careful, exhaustive human review to ensure the apparent
semantics match up with the intended ones.

# Reviewer Fatigue on Steroids

Of course, human review doesn't scale. Again, if you don't believe me, just go look at the spate of articles we've had about
more-senior engineers and open source maintainers burning out faster than ever because of the burden of wading through mountains
of AI code.

Reviewing is no one's favorite activity, and it also tends to get relegated to a half-awake LGTM first thing in the morning, or
a few vanity nitpicks to assuage the guilt of lingering PRs before an engineer packs up for their three day weekend. Making more
of the job about reviewing code is simply not going to end well. Human nature and laziness will win out, and code that looks right
will get shipped (arguably it's already getting shipped, based on the reports of severe incidents due to AI changes at Amazon and
friends). But code that looks right doesn't always behave right, and the more complex the application domain, the worse that mismatch
can be.

Oddly, I keep seeing the AI-stans try to claim that forcing more tedious reviewing on people is somehow shifting the human responsibility
back toward "engineering" rather than just "coding". I don't know about them, but I actually *like* writing code and solving problems;
certainly I like it much more than parsing out what a plagiarism machine fed on mediocre student projects was doing by stringing together
some unholy sequence of function calls.

Making development into LLM review drudgery isn't "liberating", though it does make it more like middle management's job description perhaps, 
which seems to be all the suits really understand anyway.

# Bottleneck Detected Between Keyboard and Chair

I intetionally held off from discussing what LLM advocates always offer up as the panacea to this exponential scaling of slop review -
in their eyes we just have to scale up by letting agents handle more of the review process. Simply create code review agents to validate
the code your coding agents spit up. Simple! Genius!

I wish I could poke holes in their confidence by pointing out that we don't let *humans* review their own code for a host of good reasons, 
but after watching multiple engineers fall victim to this kind of thinking, I suspect it stems from a deeper psychic wound.

Personally, I think the contagion stems from the fact that LLMs are marketed as being so much more *productive* than a human developer,
with the implication that they are therefore generating more value than a human developer, even though LLM productivity is mainly measured
by lines of code (or simple proxies for lines of code), a value metric we've known was bogus for decades. If productivity-as-quantity
becomes your guiding metric, you very rapidly hit the realization that you can't churn out code nearly as fast as you want to because of
all the non-coding bottlenecks in the system.

In fact, every human-in-the-loop system has one huge, glaring bottleneck: the human. 

Since you've already ceded ground to letting the AI write code for you, it becomes that much easier to talk yourself into letting it 
review the code too - after all, it's probably a separate instance, or you can use a different model, or different context, or a different 
agent... And then why bother spending all this time running a full regression test suite on every check-in? You could probably have some 
automated agent select which tests are most informative based on the PR diffs, so that will cut down on turnaround delay. Then of course 
you need some LLM automation to deploy to production, plus AI monitors, and an agentic way to roll back bad code or roll out hotfixes...

You might think I'm joking or exaggerating for effect, but I have heard every one of those proposals discussed with stone-faced
seriouness by people with Principal and VP in their titles. Often they've all been suggested in rapid sequence in the same meeting.

And don't get me started on the proposals to have an LLM document the code with minimal oversight. "Dictated but not read", indeed.

# Friction is Good, Actually

Mistaking quantity for quality is only one of the issues at play here, though. 

# Mean Revulsion

<!-- Most training code is bad. -->

# Software as Theory, Software as Process

<!-- Code is not really and end product, it's just the physical embodiment of a solution to a problem, and it must change
along with our understanding of the problem, and with the problem itself as it evolves. Generating code doesn't actually
generate a solution to the problem. -->

# Software as Social Activity (The Bus Factor)

<!-- Delegating to an LLM ruins your bus factor. Separate instances won't even have the context to explain what's going on,
even generously assuming the original could. LLM code is instant legacy code, and fixing it or changing it requires the
same sort of tedious review and disentangling that the rest of the stack demans for safe deployment. -->
