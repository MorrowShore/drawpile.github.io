---
layout: drawpile_post
title: "Dev Update: Week 14 of 2025"
date: 2025-04-06 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last week have seen more developments on the next version of Drawpile. [Last week's dev update](/devblog/2025/03/29/dev-update){:target="_blank"} already included some new features and plans.

## Color Marking

You can now mark layers and key frames with colors. You can set them either by right-clicking on them, using the Layer or Animation menu at the top, or through their properties.

The colors are actually stashed in the layer name, so older clients will just see a funny prefix, but they'll correctly carry it through. It will also save to and restore from ORA files correctly even when you runt them through another program.

This is available [in the alpha release](https://github.com/drawpile/Drawpile/releases/tag/continuous){:target="_blank"}.

![Layer and key frames with color markings]({{ "/assets/img/2025-04-06_colormarkers.webp" | relative_url }})

## Paint Modes

Wash mode has been fixed to work like it does in other programs. The difference is mostly visible for very soft brushes, which didn't build up any opacity at the edges, causing a strange effect. Now they do build up, but only to the maximum opacity at the point of that stroke.

The previous paint mode is still available as "Indirect Soft (Drawpile 2.2)". And, since we got one free in the protocol, there's also the mode used in Drawpile 2.1 in the form of "Indirect Build-Up (Drawpile 2.1)", which is basically just an opacity cap for classic brushes.

This will be coming in Drawpile 2.3.

<video controls>
  <source src="{{ "/assets/vid/2025-04-06_paintmodes.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Markers and Density

There's two new blend modes that work similarly to each other.

One of them is "Greater Density", which only draw if the stroke has a higher opacity than what's on the layer. It works similar to Krita's "Greater" mode or CSP's "Compare Density", minus the glitches in that when you draw with different colors. This was suggested by Annoy.

The other mode is "Marker", which will also only increase opacity, but it will always blend colors. This works like SAI's marker tool and also sorta similar to Krita's "Alpha Darken" mode, except that one has glitches when using different colors again. This one appears to be a missing division in their algorithm, so this will probably get contributed back one way or another. This was suggested by [cromachina on GitHub](https://github.com/drawpile/Drawpile/issues/1446){:target="_blank"}.

This will also be coming in Drawpile 2.3.

<video controls>
  <source src="{{ "/assets/vid/2025-04-06_greatermarker.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Other Drawpile 2.3 Things

You can now pick any blend mode for MyPaint brushes. Previously you could only choose Normal, Erase and Recolor.

You can toggle alpha preserve on brush, fill and transform blend modes. I don't really know why you'd want to, since it'll just give you a mixture of the chosen blend mode and Normal, but it's there for if you're used to Krita.

You can now use color dynamics for MyPaint brushes in indirect modes.

## Minor Additions and Bugfixes

Pixel brush outlines now snap properly when moving the cursor and soft brush outlines don't snap at all anymore. This was reported by Meru [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Session operators can now create annotations beyond the 256 maximum. This works like it does for layers, first using annotations owned by user 0 (the server) and then down from 255. This was reported by Devil Like Me [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Server owners can now specify forbidden user and session names via a regular expression. This was suggested by Bluestrings.

You can now unlist individual sessions, making them only joinable via direct links. They also can't be announced on listing servers. Server owners can toggle this per session. You can also choose to make all sessions or sessions hosted via web browser unlisted by default. This was suggested by Bluestrings.
