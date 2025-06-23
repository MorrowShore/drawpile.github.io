---
layout: drawpile_post
title: "Dev Update: Week 21, 22 and 23 of 2025"
date: 2025-06-08 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last three weeks have been extremely busy for me, hence the lapse in dev blog posts, but there's still been a good amount of development. I've also gotten the chance to meet up with some other graphics software developers, such as from Krita and GIMP, which was nice.

Everything here is available in the alpha release, which you should especially update to if you were using a previous alpha release, since several bugs and incompatibilities have been fixed: <https://github.com/drawpile/Drawpile/releases/tag/continuous>

## Lasso Fill Tool

There's now a new "lasso fill" tool. It works similar to performing a lasso selection and then filling it, except that it fills directly. It also lets you use the same stabilizer as for brushes, letting you make smoother shapes.

Lasso fills are previewed locally first, just like the flood fill tool. While this isn't really necessary to avoid "flashbanging" issues, it reduces the amount of data that needs to be sent over the network and makes quick undos instantaneous, so I think it makes sense to have. When you start another fill, switch tools, press the apply button or hit enter, the fill will become visible for everyone, so you normally shouldn't notice any difference.

This was suggested by EvilKitty3 [on GitHub](https://github.com/drawpile/Drawpile/issues/324){:target="_blank"}, Juzeror[on Discord](https://drawpile.net/discord/){:target="_blank"} as well as Fallen, Geese and MorrowShore directly. It's backward compatible and available in sessions hosted with Drawpile 2.2.

<video controls>
  <source src="{{ "/assets/vid/2025-06-08_lassofill.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Smudge Sync

You can now toggle the option to synchronize smudging with the brush. This is slower, but can help avoid artifacts when doing fast strokes with brushes that want to pick up color very frequently.

This was brought up by Donatello and xxxx. It's backward-compatible and available in sessions hosted with Drawpile 2.2.

<video controls>
  <source src="{{ "/assets/vid/2025-06-08_syncsmudge.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Classic Smudge

Classic brushes now let you enable "real" smudging with transparency, like MyPaint brushes do. This will make the smudge behave like you would expect from any other brush engine, without having to toggle that "pick color from canvas" option or ending up with fully opaque strokes when trying to smudge transparent areas. Setting the smudge to a fractional value will also mix the smudge color with the brush color, like it works pretty much everywhere else.

To be able to tell apart the two modes, the smudging slider changes its title to "blending" when the old option is selected and "smudging" for the new one. I think the idea of changing the slider title makes sense, but maybe there could be a better word for this, since it's pretty hard to translate to other languages this way.

For compatibility, existing brushes with smudging set to a non-zero value will default to the old smudge mode, new brushes and brushes without any smudging turned on will use the new one. Once you save your brushes once, they will remember the mode you actually set them to.

This was suggested by xxxx. It's *not* backward-compatible, so you can't use it in sessions hosted with Drawpile 2.2.

<video controls>
  <source src="{{ "/assets/vid/2025-06-08_smudgealpha.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Escape Selections

The "cancel action" action now gets rid of selections. The default shortcut for this is Escape. If you don't like this, you can disable it under Edit → Preferences, in the Tool tab. This was suggested by Ranny Bergamotte.

By default, deselect is also now bound to both Ctrl+Shift+A and Ctrl+D. The latter is kind of weird, but apparently some other drawing programs bind it this way, so Drawpile now does so too for brain compatibility.

![Escape to deselect setting]({{ "/assets/img/2025-06-08_deselect.webp" | relative_url }})

## Minor Additions and Bugfixes

Many 2.2 compatibility mode issues have been fixed. Some of these can cause desynchronization and sessions to become unjoinable until manually reset, so you should update to the latest alpha version if you were using those.

Pigment mode smudging handles its opacity properly. Previously it would end up doubling up on it compared to Normal mode. This was reported by xxxx.

The drawpile-timelapse command now has a `-B`/`--background-color` option to let you override the canvas background color. This was suggested by Bluestrings [on Discord](https://drawpile.net/discord/){:target="_blank"}.

You can now open and export files in the Quite Okay Image format (QOI). This mostly fell out of testing if it would make DPCS or session reset sizes smaller, which it didn't, but since it's very little code and doesn't require additional dependencies, the format is allowed to stay in this form.
