---
layout: drawpile_post
title: "Dev Update: Week 45 of 2024"
date: 2024-11-09 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last week has mostly been spent releasing Drawpile 2.2.2-beta.4, but there's also a new color circle dock implemented.

## 2.2.2-beta.4

You can find the release announcement here: <https://drawpile.net/news/release-2.2.2-beta.4/>

At the time of writing, the F-Droid version has also built successfully, but is yet to be released by them. That will probably happen next week.

## Color Circle Dock

There's a new color circle dock that's accessible via View → Docks → Color Circle. When you enable it, it may show up in a tab behind another dock.

This dock is similar to the artistic color selector in Krita or the HSV/HSY wheels in MyPaint. It lets you pick the hue and saturation/chroma in the form of a classic color circle and the value/lightness/luminosity as a slider. It adheres to your current color space (HSV, HSL or HCL), just like the color wheel and sliders do.

You can configure this color circle to preset hue, saturation or value as distinct steps, letting you pick how many steps you want to have. The hues can also be shifted to start at a different angle.

The most interesting part about this dock is probably the gamut masks though. This puts a shape on top of your color circle, letting you stick to a certain range of colors when painting or making color schemes. There's a bunch of preset masks available. Currently, there's no way inside the application to add new gamut masks, but you can create a `gamutmasks` directory [in Drawpile's application data directory](https://docs.drawpile.net/help/tech/customassets#finding-the-application-data-directory){:target="_blank"} and stick SVG files in there, which will be picked up automatically.

The video below shows it all off.

<video controls>
  <source src="{{ "/assets/vid/2024-11-09_colorcircle.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Minor Additions and Bugfixes

The login dialog no longer spills off-screen on devices with small screens when it switches to the session view.
