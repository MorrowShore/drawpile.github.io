---
layout: drawpile_post
title: "Dev Update: Week 36 and 37 of 2024"
date: 2024-09-14 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

In the last two weeks, most development work has gone into expanding on brush slots and presets. A few other fixes and features also made it in there along the way.

## Brush Attachment

Brushes now get attached to the slot you put them into, making their thumbnail show up next to the stroke preview. When you modify the brush, these changes are now automatically remembered in the brush, as indicated by the pen icon in the top-left corner. When you switch to a different brush and back, it will reload that changed state. Reverting the brush to its original state is now done explicitly, using the revert button in the top-left of the brush dock. Attaching the same brush to two different slots still works, they won't overwrite each other.

The brush editor now also lets you modify the thumbnail, name and description of the brush, rather than having those be in a separate dialog. You can save your changes into a new brush or overwrite the existing one from there.

This is how Krita and most other drawing programs handle this type of thing, so it should make it easier to switch between those and Drawpile. If you prefer the old setup, you can either disable attached brushes in the tool preferences altogether or detach individual slots using the menu at the top-left of the brush settings dock.

<video controls>
  <source src="{{ "/assets/vid/2024-09-14_brushattach.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Playing Slots

The number of brush slots is now configurable in the tool preferences. The default is still 5, but you can go up to 9 brush slots. The eraser slot is everpresent.

When you set the slot count to 1, the slot will show a brush icon instead of a number, making it look a bit nicer.

This was suggested by vipperz [on GitHub](https://github.com/drawpile/Drawpile/issues/1141){:target="_blank"}.

<video controls>
  <source src="{{ "/assets/vid/2024-09-14_slotcount.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Stream Reset Info

The server now provides more information about the reset state of sessions in the admin interface to help in debugging potential issues with streamed resets (you can read more about that [in the last devblog entry]({% post_url 2024-08-31-dev-update %}).)

The dpwebadmin UI has been [updated to version 0.6.0](https://github.com/drawpile/dpwebadmin/releases/tag/0.6.0){:target="_blank"} to show this information.

## Minor Additions and Bugfixes

Huion tablets should no longer randomly draw full-pressure strokes. This was due to them emitting spurious mouse clicks, which Drawpile now ignores. This was reported by Blozzom, DT and and Dumb Dog Disease [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The brush import now supports old (version 1 and 2) MyPaint brush packages. If you were getting an error about a brush pack not containing valid JSON, those should now import correctly. This was suggested by bunnie [on Discord](https://drawpile.net/discord/){:target="_blank"}.

There's now a Toggle Layer Visibility action available in the Layer menu. The primary point of this is so that you can assign a keyboard shortcut to it. This was suggested by incoheart [on GitHub](https://github.com/drawpile/Drawpile/issues/1366){:target="_blank"}.

MyPaint brushes now remember the input and output ranges you had configured in the brush editor, rather than reverting to whatever fits the curve. This was suggested by Verdrusk directly.
