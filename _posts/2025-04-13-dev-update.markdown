---
layout: drawpile_post
title: "Dev Update: Week 15 of 2025"
date: 2025-04-13 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last week had more work on the stuff to come in Drawpile version 2.3. Everything below pertains to that.

## Bigger Brushes

The brush size limit has been increased from 255 to 1000. Since the diameter is squared, that makes them effectively around 16 times larger. The video below gives more of a feel how much larger this is by filling an entire 2000x2000 pixel canvas with a brush at size 255 versus clearing it with a brush of size 1000.

Since having overhuge brushes in shared sessions often isn't desirable, there's now a permission setting to limit the size. By default, only operators can go up to 1000, for the rest it goes up to 255, but you can pick whatever values are appropriate for your session. Trying to use a larger brush just caps the size at the maximum instead.

This was suggested by aruqhadiyan [on GitHub](https://github.com/drawpile/Drawpile/issues/1171){:target="_blank"} and ~TGS~ directly.

<video controls>
  <source src="{{ "/assets/vid/2025-04-13_brushsize.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Blend Modes

Several new "regular" blend modes have been added, which you will find in many other programs and inside PSD files: Vivid Light, Pin Light, Darker Color, Lighter Color and Difference.

The eight special blend modes from Paint Tool SAI are also available now: Shade (SAI), Shine (SAI), Shade/Shine (SAI), Burn (SAI), Dodge (SAI), Burn/Dodge (SAI), Hard Mix (SAI) and Difference (SAI). The shine one of these already existed previously and was called Luminosity/Shine (SAI), but got renamed to be in line with how SAI 2 calls them (and the "luminosity" part was nonsense anyway.) The SAI blend modes have a more vibrant or "blown out" look than the regular modes. Saving and loading these blend modes to and from PSD files in a way that's compatible with SAI is also implemented.

The code for this was largely provided by cromachina, who also gave some examples [on GitHub](https://github.com/drawpile/Drawpile/issues/1475#issuecomment-2788048470){:target="_blank"}.

## Spectral Color Mixing

The Pigment blend mode from MyPaint is also available now. This mode gives more "realistic" color mixing than regular digital paint mixing, in particular it doesn't make certain color combinations tend toward gray when you blend them together.

This uses the same kind of spectral mixing as in MyPaint, which looks a little different in some cases due to the difference between the two applications' color spaces. There seems to be a lot of enthusiasm about different kinds of color curves to use for this, so if you're into spectral color mixing and want to get a different/better kind of mixing then give a ping somewhere.

Since this kind of color mixing is really performance-intensive on brushes, there's a permission setting for it. By default only operators can use it. For layers, it's not nearly as heavy, so it's not behind a permission there.

This was suggested by chaitae [on GitHub](https://github.com/drawpile/Drawpile/issues/1323){:target="_blank"}.

![Normal versus pigment color mixing]({{ "/assets/img/2025-04-13_pigment.webp" | relative_url }})

## Minor Additions and Bugfixes

Tiles now remember who changed them across session resets and canvas compressions. This already used to be implemented in a 2.2 beta, but got thrown out again for compatibility, since 2.2 managed to get by without modifying any of the 2.1 protocol messages. 2.3 is going to change stuff anyway, so it can add this back in.

The soft Drawpile brush now skips making subpixel offsets when the brush goes beyond a certain size. This improves performance by a good chunk without perceptibly changing the result.
