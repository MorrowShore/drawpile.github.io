---
layout: drawpile_post
title: "Dev Update: Week 40 and 41 of 2024"
date: 2024-10-14 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks have mostly been spent on reworking the shortcuts system to let you assign shortcuts to brushes and making the settings for them more usable with regards to finding shortcuts and resolving conflicts.

## Shortcuttage

You can now assign keyboard shortcuts to brushes directly. Assigning the same shortcut to multiple brushes will toggle through those brushes in alphabetic order. The shrotcut is also shown when editing a brush.

All shortcuts are now in the same settings tab, rather than spread across multiple of them. Searching will search across all of them and conflicts are shown between them as well. You can also filter shortcuts by conflicts only, to help resolve those better.

Parts of this were suggested by annoy and MajorCooke.

![Brush shortcuts settings]({{ "/assets/img/2024-10-13_brushshortcuts.webp" | relative_url }})

## F-Droid Submission

Drawpile has been submitted to [F-Droid](https://f-droid.org/){:target="_blank"}, a store application available for Android that serves free and open source software. It hasn't been accepted in yet, but it's just waiting for their review at this point. Installing Drawpile through F-Droid would be a bit easier than manually doing so via the APK and it would get you automatic updates.

You can track [this GitHub issue](https://github.com/drawpile/Drawpile/issues/1372){:target="_blank"} if you want to follow it, suggested by danmou.

## Minor Additions and Bugfixes

Double-clicks when toggling layer visibility and check state no longer get ignored. This was reported by Chryssabliss.

MyPaint brushes no longer sometimes ignore the first stroke made with them. This could also occasionally lead to the preview ending up blank.

You can now choose between a round and square expansion shape for flood fills, the magic wand and when altering a selection. The square shape is particularly useful for pixel art. This was suggested by BigCheese [on Discord](https://drawpile.net/discord/){:target="_blank"} and MorrowShore [on GitHub](https://github.com/drawpile/Drawpile/issues/1331){:target="_blank"}.
