+++
title = "GITR: Embracing Your Editor"
date = "2026-08-25T21:52:51-07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Briar"
#authorTwitter = "" #do not include @
#cover = ""
tags = ["Course", "GITR", "Text", "Editing", "Text Editor"]
keywords = ["Educational", "Resources"]
description = "Programmers are secretly writers."
showFullContent = false
readingTime = false
hideComments = false
+++

Hi! Hello! I'm finally getting back to [this thing](../growing_into_the_role). Sorry it took so long,
but inspiration is a cruel mistress.

So, what's the topic for today? Well, if you don't already know, you'll learn fairly quickly once you 
start a job in software that developing software requires a *lot* of writing. 

You write code. 

You write tests.

You write configuration files. 

You (better) write documentation.

You write design documents and emails and Slack messages...

See what I mean?

So with all this writing, you need to get comfortable with both the heart and soul of
written communication. While I can't exactly pin down which exactly is the heart and which is the soul, what
I mean is that writing involves both being able to clearly communicate your thoughts and intentions
(and yes, this includes coding because what is programming but saying very explicitly and precisely
what you want a machine to do?) **and** the seemingly-mundane aspect of actually writing the words.

*Seemingly* is the important qualifier there - yes, it ultimately becomes a mostly mechanical process of
translating thought to text via an editor once you get used to it, but when something like 80%+ of your
actual work is writing, the details matter a lot to both your overall quality of life and your efficiency.

Don't get me wrong, I don't think about efficiency in the sense that someone who writes 1000 lines a day
is better than someone who writes "merely" 800. Rather, I'm thinking of something like this - can you remember
a time you were just trying to get a command or an online example copied and the damn mouse cursor kept jumping
around and the program couldn't figure out what piece you wanted and if you clicked off you'd have to re-highlight
the whole thing and so you fumbled around with it until you were frustrated?

Just me? 

Well, I do hate Windows' notion of "helpful" text selection with a passion for this very reason, but at any rate, 
point-and-highlight text manipulation is almost as painful as hunt-and-peck typing, and the pain is similarly invisible until 
you learn there is a better way

Likewise, having good integrations in your editor or IDE for syntax checking, autoformatting, code completion, and
basic command line utilities like `git` or `make` lets you further reduce fiddliness and focus on conveying your
intent instead of jumping around and trying to keep track of eight output streams at once.

# The Best Editor Ever

So, with that soapbox out of the way, I'll go on to say that you should be writing in... whatever editor you feel
most comfortable with. Provided it's not Word or Notepad, at least.

I'm genuinely not here to get into editor wars, and there are lots of good entries out there. I'm personally a fan
of Vim but emacs, VS Code, Sublime, Pycharm, even Visual Studio can all be very effective environments to work
in, so long as you take some time to really master them.

Rather than pitch a particular editor, I want to list a set of features I think you ought to be able to find and use quickly and
painlessly in your editor of choice. If you don't know how to do any of them, investigate supported features and extensions, then poke
around until you **do** know!

I will allow myself one bit of Vim-brained advice before we start at least - don't try to do these things by navigating menus or doing
surgical tweaks with a mouse. You should have keybinds or control sequences that let you rapidly handle the tasks I pose
below without even looking at your keyboard or mouse. If every substantial edit to your work is a menu safari, good luck 
remembering what you were even going to write next!

Without further ado, some things your editor should make easy:

1. Copying, pasting, deleting, and rearranging large volumes of text
2. Performing edits on arbitrary hunks of text, including vertical slices (this was what converted me to Vim)
3. Finding words/terms via regex matches, both in the current document and across opened documents (or even the whole codebase)
4. Finding semantically related objects in code (e.g. jumping to definitions, opening documentation, etc.)
5. Executing edits based on semantic relations in code, like renaming all instances of a variable consistently with a single command
6. Displaying errors/warnings for code and config files based on compiler or linter outputs
7. Changing the view/layout of text separately from editing its content (e.g. "folding" long function or structure definitions)
8. Providing completions and suggestions on demand (I personally find these distracting if enabled while typing normally...)
9. A macro system to convert common editing sequences into custom commands

Why this set? Mostly these are things I use myself, or things I find especially helpful when I need to reach for them, even if I
don't use them every day.

If I had to pull out a theme from this list, I'd say that a good editor should avoid artificial restrictions (like only selecting
text left-to-right and top-to-bottom), provide as much language- or task-specific information as possible to help you catch and
fix problems earlier, and it should be easily extensible so that you can customize it to perfectly suit your specific needs.

I wouldn't say this list is exhaustive or comprehensive, but if your editor can handle all of these things (possibly with some
plugins or modules added on to the base system) it should be able to do everything you need for that 80% of the job that's about
manipulating text.

I'll also point out that these features are only worthwhile if you know they exist and you know how to use them without having to
check Google every time an item on the list comes up... 

What that means in turn is that you should spend some time reading your editor's
documentation, working through tutorials, and just generally *practicing* with it until the common things become second-nature. 

I realize it might sound strange to recommend practicing like this, but you really should consider an editor to be a tool in the same way that
a carpenter's hammer is a tool, or a musician's instrument is their tool for making music. No one would blink if I said a cellist ought
to spend time playing and understanding their cello, but somehow we don't take the same approach with VS Code.

# More Than Just Code

Admittedly, my focus on editor features has had a lot to say about coding applications, but I mentioned in the intro that you should think
of your editor as your go-to environment for nearly all the writing you do. While I'll admit I don't think I've ever crafted a Slack or
Teams message in Vim, there's probably an integration for it somewhere (especially if I swapped to emacs...) and I should give it a try.

That said, I really do encourage you to do as much of your text-based composition as possible in your editor. The big reason is compounding
benefits - all those shortcuts and workflow tricks you've gotten into muscle memory for coding will also be there as you edit Markdown
documentation or draft an email, and indeed, different kinds of writing will help you exercise different features of your editor and
cross-pollinate between different forms of work. 

The other big benefit is focus - replying to an `URGENT: READ NOW` email from your boss
may always be disruptive, but if you can bang out a response in a sub-window, you can get back to what you were doing that much faster
than if you have to go hunting for your mail client separately.

Perhaps naively, I also hope that funneling more through a familiar interface will encourage people to do the important kinds of writing
that often get overlooked in favor of the seemingly-more-important coding, specifically documentation and context-building for a project
or body of code. Maybe, just maybe, if you have twenty minutes at the loose end of a workday, you'll think "Hey, I could bang out a couple 
of API comments for the big new feature before I leave". I guarantee other users and developers will thank you if you do...

But that's a soapbox for a later article!

# Getting More Out of Your Editor

To be clear, I don't think your familiarity with your editor should *stop* at the list above. In particular, many editors and IDEs allow
for significant customization, either through configuration files or entire configuration languages. As an example, emacs offers Emacs Lisp,
Vim has VimScript, and Neovim uses Lua, meaning that for these editors especially, you have the full power of Turing-complete languages
at your disposal to modify and customize what they do!

This level of customization can do truly impressive things - with a little playing around you can easily create auto-close
functions for parentheses and braces (without downloading a 5MB plugin!), develop customized snippets and boilerplate generators that can be
keyed off file type or keybinds, load separate functions/features based on detected language... a genuinely impressive amount of setup and
startup can be totally automated.

The catch is that you'll need to take time to learn your editor's configuration language/settings, and there is often less scope for a direct
transfer of this knowledge to other environments. Even I have to admit that your options for using VimScript in the world outside the editor are...
slim. Still, it's worth it to at least learn the outlines, if only so you have a chance of spotting that a plugin is up to something fishy.

# A Challenge for the Pros

While this series is aimed at developers who are newer to the field, I want to give more experienced folks a reason to read along (other than
to tell me how wrong I am), so going forward I'm going to try and pose some challenges related to the main theme of each GITR article that
should be more interesting for those who already have a grasp of the basics.

This time, my challenge is to crack open a reference to your edtior's configuration language (or the list of available settings if it's config-only)
and try to create a small feature that encapsulates something you do frequently in your editor, but which isn't natively supported.

If you already have a plugin or extension for that, see if you can't fully or partially replace it with your own approach. I think you'll be
surprised how much mileage you can get out of only a handful of lines of code.

If you're really good, my challenge is to think up something you don't like about your editor, or something you wish it would do differently
and then develop your own plugin to make that a reality. Who knows? Maybe lots of people share your frustrations and it'll be something that
winds up being generally useful.

# Resources

*The Pragmatic Programmer* has a lot of good commentary on software-as-writing and to give credit where it's due, it was this book that really
got me thinking about the fundamental role of text for developers in the first place. 

As far as editor configurations go, I can only speak directly to Vim at the moment, but I worked through 
[Learn Vimscript the Hard Way](https://learnvimscriptthehardway.stevelosh.com/)
and it gave me some really good ideas, not just about VimScript itself, but also by teaching me about editor settings and configuration options I didn't even
know existed beforehand.

I'd encourage you to search around and see if you can find something similar for your own editor, if you're not a fan of Vim. I know the 
GNU project has pretty extensive documentation for [emacs](https://www.gnu.org/software/emacs/documentation.html) and the 
[Lisp variant](https://www.gnu.org/software/emacs/manual/html_node/eintr/index.html)
that powers its configuration engine. I'm sure other editors offer similar resources.

Happy Hacking!
