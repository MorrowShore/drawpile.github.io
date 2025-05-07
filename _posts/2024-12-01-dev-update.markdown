---
layout: drawpile_post
title: "Dev Update: Week 47 and 48 of 2024"
date: 2024-12-01 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks have been mostly smaller matters, but in turn it was quite a lot of them. Which makes the "minor changes" section in this one pretty long!

## Dock Improvements

Docks now use icons instead of text on their tabs. This makes them more compact. If you don't like this, they can be disabled via View → Docks → Show Icons on Tabs.

The "Hold Shift to Arrange" options has been replaced with an "Arrange Docks" action, either available in the dock menu or via View → Docks. When you trigger this action, the title bars of the docks turn into a text label that you can drag around, which means you no longer need a keyboard to do it. This was suggested by 3rd\_EFNO [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Also, when you enable a dock and it ends up in a tab, that tab now gets activaed. This solves cases where you open a dock and it seemingly does nothing because it's hiding behind some other dock.

## Temporary Tool Switchings

There's a feature in Drawpile that lets you press and hold a shortcut key to temporarily activate a tool. The amount of time you had to hold the key down used to be fixed at a quarter of a second.

Now you toggle this behavior and adjust the time it takes for it to catch under Edit → Preferences in the Tools tab. Setting the value to zero will make it so that single-key tool shortcuts are always temporary.

This was suggested by pachuco [on GitHub](https://github.com/drawpile/Drawpile/issues/363){:target="_blank"} and 3rd\_EFNO [on Discord](https://drawpile.net/discord/){:target="_blank"}.

## Server Fixings

An extremely long-running session on the syntheticdreams server revealed a bug in the code that could crash the server. The cause of this was a session having so many past registered users that they didn't fit into a server command message anymore. This caused the message to be null, the server didn't check for this case and crashed with a classic segmentation fault. Most sessions probably won't run into this in practice, since you need several thousand registered users to have joined a session to actually reach this point, but this was a very old and popular public session.

The fix is pretty simple: just limit the amount of users sent along. It's not like the huge list is useful for the client anyway. There's also been some checks added to avoid accessing null messages like this.

This was reported by Axocrat, LABS107, Meiren, Oshmitty and possibly others I missed [on Discord](https://drawpile.net/discord/){:target="_blank"}. Also thanks Liz for helping figure out the problem.

## Minor Additions and Bugfixes

Android touch events can somehow come in without touch points, which could cause a crash because the code foolishly assumed that touching the screen involved touching the screen. That's fixed now by ignoring those touch events. This was reported by Mav [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Android will no longer enable touch drawing when it detects that you have a pen. The logic for this was swapped the wrong way round.

On old Android versions, the aspect ratio is now uncapped. Some windows still get cut off though, haven't figured that out yet. This was reported by Molderche [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Android will now warn you if an action, like opening a file or joining a session, will close the current canvas, rather than just doing it and potentially losing data. This was reported by 3rd\_EFNO [on Discord](https://drawpile.net/discord/){:target="_blank"}

Tilt input from the stylus is now supported on Android.

Modifier keys not registering for tablet events and pasting between different instances of Drawpile has been fixed on Linux with Wayland. These were reported and tested by Absolute Goober [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The server no longer pointlessly reloads TLS certificates when they didn't change. This was reported by Liz while figuring out the bug above.

The list action buttons to add and remove elements from e.g. servers or canvas shortcuts now have labels, making it clearer what they're for. There's also an edit button for canvas shortcuts now, since it wasn't particularly clear that you could edit an entry. This was suggested by Maffi [on Discord](https://drawpile.net/discord/){:target="_blank"}.

On Windows, system tablet events are now properly ignored when using KisTablet drivers. Before, they could double-up in odd ways.

Tilt and barrel rotation are now properly passed to MyPaint brushes. Previously, it would always end up transmitting excessive angles for these. This was reported by MorrowShore.
