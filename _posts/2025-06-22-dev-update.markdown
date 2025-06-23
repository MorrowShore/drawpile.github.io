---
layout: drawpile_post
title: "Dev Update: Week 24 and 25 of 2025"
date: 2025-06-08 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks were spent on some extra features needed to get the first beta of 2.3.0 on the road. The only thing really missing before it can go into translations is letting you set brush size and layer limits, which currently isn't full implemented.

If you don't want to wait, you can try out the current state [in the alpha release](https://github.com/drawpile/Drawpile/releases/tag/continuous){:target="_blank"}.

## OKLAB

There's now a new blend mode that uses the OKLAB color space for blending. It is a fancier way of doing "Normal" blending that avoids some unsightly cases when blending certain colors, such as dark halos where conflicting colors meet. You can see a comparison below that shows a particularly noticeable case of that.

Similar to Pigment mode, this type of blending is pretty heavy, so for brushes it's restricted to the same permission that only operators have by default.

This was contributed by Bonbli [on Discord](https://drawpile.net/discord/){:target="_blank"}. It's only available in Drawpile 2.3 sessions. It also bumps the protocol version.

![Comparison between Normal and OKLAB blend modes]({{ "/assets/img/2025-06-22_oklab.webp" | relative_url }})

## Harmony Swatches

The color wheel now lets you enable color harmony swatches underneath it, similar to how it works in Krita.

By default, they show adjacent hues along with two sets of shades, one set having less saturated shadows and more saturated highlights and the other going vice-versa. Clicking on a color will select it so that you can make a palette, double-clicking will also make it the new baseline color. You can configure these swatches to your liking in the same menu that you use to enable them. If you have other ideas for sensible harmonies, let me know somewhere!

This was sorta suggested by tiar.

<video controls>
  <source src="{{ "/assets/vid/2025-06-22_harmonyswatches.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Pick Your Tools

Since the drawing tools toolbar is getting kind of long at this point, you can now configure which tools you want to have on it and in what order they show up in. This is particularly useful if you have a smaller screen and the size is throwing one of your more frequently-used tools into the fold-out area.

The configuration option is under Tools → Configure drawing toolbar, under View → Toolbars and when right-clicking a toolbar.

This was suggested by MorrowShore [on GitHub](https://github.com/drawpile/Drawpile/issues/1326){:target="_blank"}.

<video controls>
  <source src="{{ "/assets/vid/2025-06-22_toolconfig.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Hide Your Tools

In small-screen mode, you can now choose to toggle the toolbars on the side and bottom through View → Always show side/bottom toolbar. When disabled, the toolbars will only show up when you hit any of the buttons on the edges. You can also no longer hide the toolbars by right-clicking on one of them and unchecking them, since that didn't actually work and would bring them back as soon as you pressed any of the buttons on the side. This was suggested and reported by CosmosX007 [on Discord](https://drawpile.net/discord/){:target="_blank"}.

There's also some extra space at the bottom-left edge, since some devices have such round corners that they cut off the undo button down there. This was reported by March [on Matrix](https://drawpile.net/matrix/){:target="_blank"}.

<video controls>
  <source src="{{ "/assets/vid/2025-06-22_smallscreentoolbars.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Minor Additions and Bugfixes

You can now assign keyboard shortcuts to tool blend modes, to let you e.g. assign Behind or Pigment mode to a keyboard shortcut. This was suggested by Izzy and Meru [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The eraser side of pens is now properly detected on Android. Since Android doesn't tell Drawpile that you're using the eraser until it hits the surface, doing so will erase with your current brush, like it works in the browser. This was reported

The connection quality setting now works properly when choosing who to pick for a session autoreset. Previously the server and client would get things jumbled between each other. Newer clients will indicate that they have fixed this issue, so just updating the server will make it work for everyone. This was reported by Blozzom and grimsley [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The web browser version of Drawpile now has a language selection and a tablet tester on its start page. The latter was contributed by Shivani [on GitHub](https://github.com/drawpile/Drawpile/pull/1501){:target="_blank"}
