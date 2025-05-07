---
layout: drawpile_post
title: "Dev Update: Week 46 of 2024"
date: 2024-11-17 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last week has seen some further updating after the beta release, as well as fixing some issues found in it and adding some smaller features.

You don't have to wait for the next beta to get it though, it's available for use and testing in the continuous release: <https://github.com/drawpile/Drawpile/releases/tag/continuous>

## Beta Updates

The server at pub.drawpile.net has been updated to 2.2.2-beta.4, meaning that it supports streamed resets now.

The web version of Drawpile at web.drawpile.net has also been updated.

## Drag Adjust Canvas Shortcuts

The size adjustment canvas shortcut (Shift+Left Click and Shift+Middle Click by default) has been made dynamic depending on how fast you drag it. Dragging faster causes larger adjustments, making it easier to get the correct brush size without having to scrub across your tablet multiple times to cover a large distance. This was suggested by MorrowShore directly.

There are also new canvas shortcuts for changing the color hue, saturation and value (or equivalent, if you're using a different color space.) They're not bound by default, but you can do so in the preferences. They work much the same like the size adjustment shortcut does, by dragging across the canvas, as seen in the video below. This was suggested by Dann DeCairns [on Discord](https://drawpile.net/discord/){:target="_blank"}.

There's also been some general rejigging of the canvas shortcuts code to make it a bit easier to work with, so if you notice any weirdness about it, please report it.

<video controls>
  <source src="{{ "/assets/vid/2024-11-17_coloradjust.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Key Frame Layer Search

The key frame properties dialog, which you get when double-clicking on a key frame in the animation timeline, now lets you search for layers. Not sure if anyone except me really uses this dialog, but it's very useful if you're doing animations with a lot of nested groups and need to adjust the visibilities inside them!

A similar implementation could be used for searching layers in the regular layer list, as is tracked [in this GitHub issue](https://github.com/drawpile/Drawpile/issues/1384){:target="_blank"}. Although not exactly sure where to put a search bar there, since we can't just take up space in such an important dock with a constantly visible search bar. If you have ideas, give a shout.

![Key frame properties dialog with layer search]({{ "/assets/img/2024-11-17_keyframelayersearch.webp" | relative_url }})

## Minor Additions and Bugfixes

Holding down a key to temporarily switch tools has been fixed, it got broken on accident. This was reported by 3rd\_EFNO and bunnie [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Transforms no longer apply bilinear interpolation unnecessarily, like for 90 degree rotations or similar. This was already fixed previously, but there was a bug introduced with the transforms rework that caused it to not recognize those cases anymore.

On Linux, the locale is now reset properly, since Qt messes it up on startup there. This was reported by Meru [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Android builds no longer require pointless camera, microphone and legacy storage permissions. This could use some testing on different devices if that kinda thing didn't break anything.

The 32 bit Android build now targets Android 6 again, rather than Android 14. While the latter is required for newer devices, those should be running 64 bit, so doing it this way should give us a wider spread of compatibility. The F-Droid version did it that way already.
