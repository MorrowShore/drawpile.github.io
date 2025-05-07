---
layout: drawpile_post
title: "Dev Update: Week 28 and 29 of 2024"
date: 2024-07-21 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks have been spent on getting the 2.2.2 beta ready. Translations for it are open, if you want to contribute some: <https://hosted.weblate.org/engage/drawpile/>

## Iconography

Some icons have been changed to make them look nicer and clearer. Several buttons in dock headers and footers also fill the available space a bit more now, making them easier to hit.

These were contributed and suggested by MorrowShore.

## Color Sliding

The color sliders now show only three sliders at a time instead of six, either HSV/HSL/HCL or RGB, with a toggle between them. This reduces the minimum size by a good chunk, which is nice for smaller resolutions. Editing both of those sets of sliders simultaneously is unlikely to be something you'd be doing normally, so this switch shouldn't cause significant burden on usability otherwise.

Since there's some extra room now, there's a color input field here now, where you can put in and copy out hex colors and similar.

This was suggested by Kawaxte and MorrowShore.

## Android Filings

Saving files on Android now first writes them to a temporary storage and then copies them over the existing file, making it less likely that you end up with a corrupted file somehow. It's still not an atomic operation, since Android doesn't support that, but a crash or the application getting terminated by the OS for being in the background for too long or something will no longer destroy both the old and new file.

Files are also truncated on write properly now. This could lead to (fixable) corruption on ORA files on Android before.

## Minor Additions and Bugfixes

A discontinuity in soft brushes now gets compensated for, which should avoid some cases of jags in tapered strokes. This was reported by BulletPepper [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Zooming with keyboard shortcuts now zooms where the cursor is pointing instead of onto the center of the view. If you like the old behavior better, there's separate "center" keybinds for it now. This was suggested by Chryssabliss.

The drawpile-timelapse command-line tool now allows cropping and cuts out pauses.

Typing an annotation or into chat should now no longer switch tools if you press their shortcut key. This was reported by leopardheart982 [on GitHub](https://github.com/drawpile/Drawpile/issues/962){:target="_blank"}.
