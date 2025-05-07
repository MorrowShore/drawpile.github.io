---
layout: drawpile_help
title:  "Frequently Asked Questions"
description: "Answers to common questions about Drawpile."
date: 2023-09-29 00:00:00 +0200
category: "help"
tag: help common
---

If you don't find an answer to your question here, either check out the other help articles on the site or ask in Discord, the web chat or on GitHub! You can find links to those [on the main help page](https://drawpile.net/help/).

* Table of contents
{:toc}

## Tablets

### Why does Drawpile not recognize pen pressure?

Your current brush may not have pressure dynamics enabled, try another one.

On Windows, there may be a driver mismatch. In Drawpile, pick a different option under Edit → Tablet Driver. Try them all and see if one of them works.

For further troubleshooting, refer to [the tablet setup and troubleshooting page](/help/tech/tablet).

![Tablet Drivers menu]({{ "/assets/img/help/tabletdriver.webp" | relative_url }})

### Why does Drawpile not switch back after I use my tablet pen's eraser?
### Why am I getting thick dots on my lines?
### Why does the brush position detach from my cursor?

All of the above usually happen on Windows because of a driver mismatch. In Drawpile, pick a different option under Edit → Tablet Driver. Usually "KisTablet Wintab" is the correct one in this case, but try them all until you find one that works.

For further troubleshooting, refer to [the tablet setup and troubleshooting page](/help/tech/tablet).

![Tablet Drivers menu]({{ "/assets/img/help/tabletdriver.webp" | relative_url }})

### Why did my tablet stop working when it was fine yesterday?

This usually happens after a Windows or tablet driver update breaking stuff. In Drawpile, pick a different option under Edit → Tablet Driver. Try them all until you find one that works. It may be one that previously didn't work!

If you're on Linux, updates to your window manager (Gnome, mostly) may break tablet integration. You can try downgrading or switching to a different window manager to check if things work again.

For further troubleshooting, refer to [the tablet setup and troubleshooting page](/help/tech/tablet).

![Tablet Drivers menu]({{ "/assets/img/help/tabletdriver.webp" | relative_url }})

### Why does Drawpile not draw when I touch my screen?

Probably because it's not set to do so. Go into Edit → Preferences, switch to the "Touch" tab. Make sure the touch mode is set to "Touchscreen" and the one-finger input is set to "Draw".

For further troubleshooting, refer to [the tablet setup and troubleshooting page](/help/tech/tablet).

![Touch input preferences]({{ "/assets/img/help/inputtouch.webp" | relative_url }})

### Why does my cursor disappear/turn into a crosshair when I use my tablet?

This happens on Linux under some Wayland environments [because of this bug](https://bugreports.qt.io/browse/QTBUG-123776){:target="_blank"}. Until they have fixed it and the fix is integrated into Drawpile, switch to an X11 session instead, you should be able to do so in your login screen.


## Online

### Why are random people joining my session?

Probably because you didn't set a password on it. You can set it on a running session under Session → Settings. You can also assign one when hosting a session.

### Why is undo and redo so delayed?

Undo operations are synchronized with the server. If you have high latency, this will cause a delay from the command having to travel from your computer to the server and back. This is usually because of physical location, if the data has to go halfway around the world, it'll take longer than for people who live next door to it.

If everyone in the session is having the issue, consider switching to a different server.

### Why can't I undo anymore after other people draw things?

Undos are shared between all users. Operators can increase the undo limit under Session → Undo Limit.

### What is this "session reset" that keeps interrupting my session?

As of Drawpile version 2.2.2, automatic session resets no longer interrupt the session. If it still happens, either the server is out of date or the operator(s) of the session are using an outdated client. Ask them to update.

The reason these resets happen is that the Drawpile server stores a history of all drawing commands made in the session. When that gets too big, the history is replaced with the current state of the canvas as a new starting point. Older versions of the server had to stop everything to perform this reset, newer versions just do it on the fly instead.


## Drawing

### Why does Drawpile not save the changes to my brush?

This was changed in Drawpile 2.2.2. If your brush forgets its changes, you probably need to update.

### Why does the fill tool just fill a square?

This is the "size limit" option in the fill tool settings. Raise it up to the maximum to get an unlimited fill. If this doesn't work, you're on an old version of Drawpile and need to update.

### Why do layers with blend modes like Multiply not work inside layer groups?

Drawpile preserves alpha for blend modes other than Normal and Erase. You may be used to this as "clipping groups" from other programs. If you don't want this, set the group's blend mode to "Pass Through".

For more information, check out [the full article on alpha preserve](/help/draw/clipping).

### How can I use clipping groups/inherit alpha in Drawpile?

In short: put your layer in a layer group, put another layer on top and set its blend mode to anything other than Normal. Recolor is like Normal but with alpha preserve.

For a long explanation, take a look at [the full article on alpha preserve](/help/draw/clipping).

### How can I alpha lock a layer?

In Drawpile, you alpha lock your brush, not the layer. For classic brushes, you do this by changing the brush's mode from Normal to Recolor. For MyPaint brushes, activate the lock button.

If you really need an alpha-locked layer, you can use another layer in Recolor mode. For more information on how to do that, check out [the full article on alpha locking](/help/draw/clipping#alpha-lock).

![Recolor mode on brushes]({{ "/assets/img/help/recolor.webp" | relative_url }})

### How do I change the DPI/PPI?

This question comes up pretty regularly, but it's usually because of misconceptions and misinformation about what DPI/PPI actually do.

DPI stands for "dots per inch", PPI stands for "pixels per inch". They measure how many pixels a printer would put into an inch of paper.

Drawpile doesn't support printing and it always measures canvas sizes in pixels, never inches or centimeters. That means changing the DPI/PPI wouldn't do anything, it's meaningless in digital drawing.

If what you're actually trying to do is change the canvas size, go into "Edit" in the top menu and click on "Resize Canvas". There you can change the size.

### How do I turn off the canvas rotation snapping around zero degrees?
### How do I make the canvas rotation snap harder?

Under Edit → Preferences, in the Shortcuts tab under Canvas, you can change the "rotate" options to "free rotate" instead. For touch rotation, it's in the Touch tab, where you can also change it from "rotate" to "free rotate".

If you want it to snap harder, set it to "ratchet rotate" instead to get rotations in fixed increments.


## Tech

### Why does Windows say it protected my computer from Drawpile?

Make sure that you download Drawpile [from the drawpile.net download page](https://drawpile.net/download/) or [from the Drawpile project GitHub releases](https://github.com/drawpile/Drawpile/releases). Other places are not provided by Drawpile and are not safe.

If the warning shows up anyway, then it's probably because a new Drawpile version has been released recently. The warnings are nonsense and stop after a few weeks usually. There's nothing wrong with Drawpile, Microsoft just wants us to pay them $400 per year for them to not do this.

### Why does my antivirus software flag or quarantine Drawpile?

Make sure that you download Drawpile [from the drawpile.net download page](https://drawpile.net/download/) or [from the Drawpile project GitHub releases](https://github.com/drawpile/Drawpile/releases). Other places are not provided by Drawpile and are not safe.

If it happens anyway, then it's probably because a new Drawpile version has been released recently. Antivirus software will consider it an unknown piece of software and react viciously. They usually stop after a few days, once they have analyzed it.

### Can I use Drawpile on my Android device?

Yes, it's available [on the Download page](https://drawpile.net/download/#Android) and should work on both tablets and phones. It's not currently available on the Play Store.

### Can I use Drawpile on my iPad?

You can use the web browser version. See [the Browser section on the Downloads page](https://drawpile.net/download/#Browser).

For a native application, Apple doesn't allow Drawpile in its App Store. There may be hope with regards to Apple being forced to open up their platform or via sideloading, but currently we don't have any developer that has the necessary devices and subscriptions to even try it out.

If you know someone that does send them our way! The [main help page](https://drawpile.net/help/) lists ways to get in contact.

### Is there a browser version of Drawpile?

Yes, see [the Browser section on the Downloads page](https://drawpile.net/download/#Browser).

### What programming language is Drawpile written in?

Drawpile consists of several pieces in different languages.

The core paint engine is written mostly in C and a bit of Rust. The frontend for the client and the server are both written in C++ using Qt.

The list server is written in Go. The website is in Python. Web frontends are in TypeScript.


## Project

### Can I donate money to Drawpile?

Thanks for considering it! It's currently not possible to do so, since we don't have the necessary organizational structure.

### Can I contribute to Drawpile?

You can! Documentation (like this article), translations, bug reports, feature suggestions and code contributions are gladly taken. For ways to get in contact, check out [the main help page](https://drawpile.net/help/).

Hanging out on Discord or IRC to help people who have questions also helps.
