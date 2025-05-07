---
layout: drawpile_post
title: "Dev Update: Week 30 and 31 of 2024"
date: 2024-08-04 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The builds for Drawpile 2.2.2-beta.2 are now available to try out! Still gotta write a release announcement, make a Flatpak build and some other stuff, but you can grab them already.

In fact, you *should* grab them! If you find any major issues before the announcement is sent out to everyone, you might save someone some trouble or even lost art. So early testing is always appreciated.

# Beta Release

Before you grab the release, consider taking a look at [how you can enable crash dumps](https://docs.drawpile.net/help/tech/crashdumps){:target="_blank"}! If you have these enabled and Drawpile crashes, you'll get a file that you can send to a developer that may let them figure out what went wrong. This only works if you turn it on *before* Drawpile crashes, otherwise that information will be lost to the aether (or, more likely, sent to Microsoft, who will just ignore it.)

You can download the beta at <https://github.com/drawpile/Drawpile/releases/tag/2.2.2-beta.2> (the downloads are at the bottom where it says Assets.)

The version is called "beta.2" because there was already a server-only beta.1 shortly after 2.2.1 came out, which contained some stuff to support the browser version of Drawpile on the public servers. So this is the first *interesting* beta, you didn't miss anything.

## File Formats

It took several days of fighting against Windows to get the libraries needed for additional file format support to work there, but now they're in there.

You can now export animations as MP4 videos, WEBM videos and animated WEBP images. If in doubt, MP4 is the most compatible of these.

Opening and saving WEBP as still images is also supported now. We'll probably start using WEBP in Drawpile internally for some stuff, since it's smaller and faster than compressing images as PNGs, so libwebp is now a mandatory dependency.

## Minor Additions and Bugfixes

High-DPI scaling is now enabled by default on all platforms except Android.

If the server supports it, session resets will now no longer pop open the chat and instead show a HUD message about it. Older servers still send them as a regular alert though.

Kinetic scrolling on Android is now done via touch only, not via the pen, since that caused some trouble with stuff like moving layers or operating pressure curves.

You can now import role lists with more than 100 users on them, since that's something that long-running public sessions need. This was reported by kale [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The dedicated server now lets you change a session's founder name to something else via the web admin API.
