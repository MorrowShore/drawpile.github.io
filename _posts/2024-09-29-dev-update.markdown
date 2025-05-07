---
layout: drawpile_post
title: "Dev Update: Week 38 and 39 of 2024"
date: 2024-09-29 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks have been spent with some new hardware in the form of a Surface tablet, which allowed several fixes and additions in regards to UI scaling and touch features.

## Color Picker Sampling Circle

The color picker now shows the picked color in a preview on the canvas.

![Color picker sampling circle]({{ "/assets/img/2024-09-29_samplingcircle.webp" | relative_url }})

You can toggle this under Edit → Preferences, under the Tool tab.

![Sampling circle preferences]({{ "/assets/img/2024-09-29_samplingcirclesettings.webp" | relative_url }})

## Rotation Snapping

Canvas rotation now snaps automatically around 0° when using the canvas shortcut or touch to rotate the canvas. This makes it easier to reset the rotation without having to use a separate shortcut, with a small enough range that you don't lose any rotation angles that are actually useful.

If you don't like this, you can change the canvas or touch shortcut to "free rotate canvas" instead, which will disable this behavior.

## Touch Gestures

Drawpile now supports more kinds of touch gestures. They have been moved to their own tab in the preferences, where you can pick the behaviors.

By default, you can tap the canvas with two fingers to undo and with three fingers to redo. Tapping it with four fingers will hide and show the docks around the canvas. Tapping and holding one finger in place will summon the color picker, indicated by the color sampling circle around the touched spot.

![Touch gesture preferences]({{ "/assets/img/2024-09-29_touchsettings.webp" | relative_url }})

## Minor Additions and Bugfixes

The browser and Android will now guess whether to enable touch drawing by if it received a pen event. That means if you have a phone or tablet without a pen, touch drawing will be enabled, if you do have a pen, just moving it over the canvas will disable it. This was suggested by TomTheDragon in person.

Several issues with UI scaling have been fixed, relating to dock toggling, brush outline scaling and others. This was reported by annoy in person.

The layer view mode will now render the active layer truly in isolation, disregarding the parent opacity, visibilities and blend modes. This was reported by MachKerman [on Discord](https://drawpile.net/discord/){:target="_blank"} and incoheart [on GitHub](https://github.com/drawpile/Drawpile/issues/1371){:target="_blank"}.

There is also now a group view mode that will show the parent group of the active layer. This was suggested by Rylan [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The server API now has endpoints for checking session and user passwords, allowing external tools to check those.
