---
layout: drawpile_post
title: "Dev Update: Week 16 and 17 of 2025"
date: 2025-04-27 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks were mostly spent on changing the way layer and selection ids work for Drawpile 2.3.0. This works towards removing layer count limits and allowing selections to mask brush strokes.

## Old Mac Support

There's now a build for old macOS versions available [in the Extras section of the download page](https://drawpile.net/download/#Archive){:target="_blank"}, as `Drawpile 2.2.2 for macOS older than 12 (Monterey)`. So if you have an older Mac or know someone that does and couldn't update Drawpile, this should make it run again.

Thanks Geese for letting me have at his laptop over the holidays for this.

## Layer Identification

Previously, layer ids used to be 16 bits long in the network protocol. The top 8 bits were used for the creator's user id and the bottom 8 bits were the "element id" for the layer. This gave a limit of 256 layers per user. Operators were able to create layers for other users, so they could sort of escape this limitation that way.

Now layer ids have been increased to be 24 bits long, with the bottom 8 bits referring to the user id still and the top 16 bits determining what element the id is referring to. If the topmost bit is 0, the 15 bits thereafter are the layer's element id, meaning the limit is now 32767 layers per user. There'll probably be a limit setting added to let session operators restrict the amount of layers each user can create, since regular sessions are unlikely to require tens of thousands of layers per user. Even the old limit of 256 is kind of high for public sessions, usually leading to them not allowing layer creation at all.

If the top bit is set, the id refers to a selection instead, which internally is also sort of a layer and to it makes sense for many commands to be able to target them like this. This also gets rid of some special hacks for the transform commands that were used to make them refer to selections, they can just do this via normal means now.

Drawpile also now considers any message that refers to selections 1 to 127 as local-only and 128 to 255 to be sent over the network. This isn't terribly exciting yet because the only selection id currently in use is id 1, but the idea is to use this to let selections mask brush strokes without having to always send every selection over the network, since doing so for selections that don't have a visual effect is pointless. Instead, the client can only send tiles that are actually necessary for masking as the brush strokes are made.

## Selection Drawing

Selection masks can now be drawn on using Selection → Draw on Selection. This works similarly to the select/deselect pen in SAI, the global layer mask in Krita or the quick mask in GIMP.

You can also change the color of the selection mask now, via View → Set Selection Mask Color.

<video controls>
  <source src="{{ "/assets/vid/2025-04-28_drawonselection.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Minor Additions and Bugfixes

Fixed a rare crash that could occur if you had many brushes with the same name assigned to the same shortcut. This was reported by Blozzom [on Discord](https://drawpile.net/discord/){:target="_blank"}.
