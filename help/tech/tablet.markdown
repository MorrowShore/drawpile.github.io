---
layout: drawpile_help
title:  "Tablet Setup and Troubleshooting"
description: "Help with tablet issues in Drawpile."
date: 2023-10-26 00:00:00 +0200
category: "help"
tag: help tech
---

This article tries to help with tablet issues. If you can't get things working on your own, ask for help on Discord, in the web chat or on GitHub! Take a look at [the help page](https://drawpile.net/help/) for links to those.

If your brush doesn't react to pressure, make sure that the one you're using actually has pressure dynamics enabled. Try switching to different brushes to see if they all behave the same way.

You can also use the tablet tester under Help → Tablet Tester. Use your tablet to draw in the grid area on the left. Red means it's detected as mouse input. Green or blue is for tablet input, which will vary in thickness depending on pressure. On some systems, you'll get both simultaneously. The text on the right is the same thing in text form, it's mostly useful so you can send it to a developer if they ask for it.

![Tablet tester]({{ "/assets/img/help/tablettester.webp" | relative_url }})

If pressure or the eraser on your pen isn't working, make sure that those are actually enabled in the settings. You can do so under Edit → Preferences in the Tablet tab under the "Pen pressure: Enable pressure sensitivity" and "Eraser tip behavior" settings.

![Tablet input preferences]({{ "/assets/img/help/inputenable.webp" | relative_url }})

Further troubleshooting is different depending on which operating system you are using:

* [Windows](#windows)
* [Linux](#linux)
* [macOS](#macos)
* [Android](#android)

## Windows

On Windows, there's multiple ways tablets can send inputs to an application. Drawpile has a few different drivers for this. If there's a mismatch between it and your tablet driver, you can get weird effects, such as pressure not working, the eraser on the pen not being (un)detected properly or full-pressure blobs sometimes appearing.

In that case, try out different drivers in Drawpile under Edit → Tablet Drivers (if you don't see this, you're either not using Windows or your version of Drawpile is outdated and you should update.)

![Tablet Drivers menu]({{ "/assets/img/help/tabletdriver.webp" | relative_url }})

<div class="notification">
    <strong>Note:</strong> Always try <em>all</em> of options in the Tablet Drivers menu! Even if you think your tablet is configured to use either Windows Ink or Wintab, it might not <em>actually</em> be using that for Drawpile. Some tablets try to be "smart" and detect which input an application wants.
</div>

If none of the options work, you may have to toggle the Windows Ink or Wintab option in your tablet's settings. On Wacom tablets, this is done in the Wacom Tablet Properties in the "Mapping" tab by toggling the "Use Windows Ink" option. Other tablets usually have similar settings.

![Wacom Tablet Properties Windows Ink option]({{ "/assets/img/help/wacomwinink.webp" | relative_url }})

Toggle the setting off if it's on or vice-versa. Restart Drawpile and try all the options in the Tablet Drivers menu again.

On **Huion tablets**, try changing the pressure curve in the Huion driver settings slightly. You can find this under the "Digital Pen" tab under "Pressure Sensitivity Adjustment" (see screenshot below.) This inexplicably fixes issue sometimes.

![Huion Pressure Sensitivity Adjustment]({{ "/assets/img/help/huion.webp" | relative_url }})

If none of that works, restart your computer and try again, sometimes tablets fail to apply their settings without it.

If that doesn't work, toggle it back the other way and restart your computer again. It's pretty silly, but some tablets (especially Huion) often suddenly start working if Windows Ink and Wintab are toggled back and forth like this.

If that still doesn't work, come ask for help on Discord, the web chat or GitHub. Take a look at [the help page](https://drawpile.net/help/) for links to those.

## Linux

On Linux, usually tablets just work. If not, you may have to install a package such as libwacom on Wayland or xf86-input-wacom on X11. Refer to your distribution's documentation for how to do this.

If you're having problems in Wayland with your cursor disappearing or turning into a crosshair when you use your tablet, switch to an X11 session instead. You should be able to do that in your login screen.

If you can't get it working, you can ask for help on Discord, in the web chat or GitHub. Take a look at [the help page](https://drawpile.net/help/) for links to those.

## macOS

There's no known issues with tablets on macOS. If you have trouble anyway, ask on Discord, in the web chat or GitHub. Take a look at [the help page](https://drawpile.net/help/) for links to those.

## Android

Drawpile will try to recognize if your device has a pen or not and enable touch painting accordingly. If it gets it wrong, you can change it under Edit → Preferences in the Touch tab under the "One-finger input" setting. Make sure the touch mode is set to "Touchscreen" if you want to draw by touching.

![Touch preferences]({{ "/assets/img/help/inputtouch.webp" | relative_url }})

If you have other issues, ask for help on Discord, in the web chat or GitHub. Take a look at [the help page](https://drawpile.net/help/) for links to those.
