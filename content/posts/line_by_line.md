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
<Inserting at line 3>
> You see, MUCK servers run off the telnet protocol, which means their interface is line-oriented.
> .print
About that text editor - the first, last, and only one?
There's something a bit weird about it.
You see, MUCK servers run off the telnet protocol, which means their interface is line-oriented.
When you press <kbd>Enter</kbd>, you're officially done with your current input.
<Inserting at line 4>
...
```

As it turns out, this kind of editor is pretty simple to program. 
<!-- TODO: discussing ease of writing and tie into early Unix software -->

# Rolling Your Own Unix Tools
