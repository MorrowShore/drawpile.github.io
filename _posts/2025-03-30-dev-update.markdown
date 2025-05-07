---
layout: drawpile_post
title: "Dev Update: Week 12 and 13 of 2025"
date: 2025-03-30 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

Drawpile 2.2.2 has been released last week and work has begun on version 2.3.0.

## Drawpile 2.2.2 Release

If you haven't yet, take a look at [the Drawpile 2.2.2 release announcement](https://drawpile.net/news/release-2.2.2/){:target="_blank"}. It's out now ready to update to.

There's also [this illustrated guide](/help/common/update2x2x2){:target="_blank"} of the stuff that changed.

Flatpak (Linux) has the new version available by now. F-Droid (Android) will probably gain it in the coming week.

## Drawpile 2.3.0 Begins

Work on Drawpile version 2.3.0 has begun. I don't really have a solid roadmap for it yet, but there's a few protocol-breaking things I want to get done in it. These need to be done first so that they can be tested thoroughly without introducing incompatibilities during testing. Which may still happen if there's a design mistake in there.

The vague idea of a roadmap for the protocol-related changes are:

* Clipping groups and explicit alpha preserve, see below.
* Fixing the indirect mode blend mode so that it doesn't look weird on soft brushes.
* Adding a "compare density" brush blend mode, which is actually the above indirect blend mode.
* Making selections mask brushes. That is, when there's a selection, the brush strokes will not spill out of it.
* Letting you use arbitrary blend modes for MyPaint brushes.
* Increasing the maximum size limit for brushes and adding a session setting to let operators limit the size.
* Annotation render modes, which [were already implemented last year](/devblog/2024/03/16/dev-update#annotation-aliasing){:target="_blank"}
* Lightness/darkness to alpha blend modes, which [were also already implemented the same week](https://docs.drawpile.net/devblog/2024/03/16/dev-update#lightnessdarkness-to-alpha){:target="_blank"}
* Removing the Drawpile 2.1.\* compatibility mode and instead adding a compatibility mode for Drawpile 2.2.\*.
* A new canvas file format in some form or another, see below.

They're overall much smaller than the changes in 2.2.2, so ideally the release cycle shouldn't be as long this time. Depending on how quickly these get done, there may be some other changes that get added in.

If you have other things you really want included, say so [on Discord or IRC or something](https://drawpile.net/help/){:target="_blank"}!

## Clipping Groups

Clipping groups is something that some programs use to implement alpha preserve. Drawpile instead implemented it by making blend modes other than Normal preserve alpha. This basically means that layer groups are also clipping groups. That tends to work fine for people coming from Krita or learning Drawpile from scratch, since the way it works is pretty simple: create a group, set the blend mode.

However, experience shows that this is difficult to understand for people coming from programs that use clipping groups instead. They tend to treat layer groups more as organizational "folders" rather than a way to create an isolated set of layers. Another issue in that regard is PSD files, which only support clipping groups, but not regular alpha preserve, meaning that interoperation via those files is pretty annoying.

To fix that, Drawpile is gaining clipping groups by letting you toggle a "clip to layer below" flag on your layers:

<video controls>
  <source src="{{ "/assets/vid/2025-03-30_clip.mp4" | relative_url }}" type="video/mp4"/>
</video>

If you're coming from a program that implements alpha preserve like this, it's obvious what's going on. If you're coming from Krita or only use Drawpile or just know how image compositing is supposed to work, it may be somewhere between getting the idea and getting a headache.

To understand what's going on, we can look at what opening the resulting ORA file of the above video looks like in Krita:

![Clipping group in Krita]({{ "/assets/img/2025-03-30_kritaclip.webp" | relative_url }})

So what enabling "clip to layer below" does is that it creates an implicit layer group encompassing the pile of layers that has the attribute set plus the layer below it, sets the blend mode and opacity of that group to that of the bottom layer, sets the bottom layer to Normal blending with 100% opacity and then stacks all the other layers on top with alpha preserve enabled. I assume this convoluted way of handling alpha preserve exists because some primordial drawing program implemented it before actual layer groups and then other programs copied it. In fact, there's still some drawing programs out there that only implement clipping groups at top-level, but not actual layer groups.

When you reopen this ORA file in Drawpile, you get the clipping groups back. Krita will obliterate the property that Drawpile sets on the group inside the ORA file marking it a clipping group, but the funky name will still lead to it being decoded properly. That is, unless you've renamed it or created a structure that is impossible, like turning off alpha preserve on one of the layers inside the clipping group, in which case you get the actual group instead.

This feature is generally working, but at the time of writing there's still some cases where the canvas doesn't refresh properly when you toggle a clipping group on a hidden layer. But that's just a matter of teaching the difference-detecting algorithm that it needs to pay attention to this.

## Explicit Alpha Preserve

Since clipping groups basically require implementing a good chunk of it already, the Krita model of alpha preserve is coming to Drawpile as well. This one isn't so crazy, it's just an explicit toggle to enable or disable alpha preserve on a layer. It's called "inherit alpha", because that's what Krita calls it.

By default, changing blend modes will still toggle this attribute accordingly. This leads to the same behavior as in previous Drawpile versions, automatically enabling alpha preserve on blend modes where that makes obvious sense. If you don't like this, you can disable it and manually manage your clipping groups or alpha preserve.

This feature is working for layers, but not yet for brushes, the fill tool or the transform tool.

<video controls>
  <source src="{{ "/assets/vid/2025-03-30_alphapreserve.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Canvas File Format

The new canvas file format [talked about in the last blog entry](/devblog/2025/03/15/dev-update.html#project-file-format){:target="_blank"} has gained some additions thanks to Bonbli [on Discord](htps://drawpile.net/discord/){:target="_blank"}. By rearranging the way the pixel data is stored, the file size has been reduced by a good bit smaller, leading to the files to be almost always smaller than the equivalent ORA file.

That means the new format is now both a bit smaller *and* over thirty times faster to save and load than ORA.

The stuff with regards to autosaving and whatnot is still left to do, but none of that is protocol-breaking. It could even theoretically be included in a Drawpile 2.2.3, should that happen for some reason. So the priority will probably be to get the protocol changes out of the way

## Scroll Fixes

Kinetic scrolling (or flick scrolling or touch scrolling or whatever you want to call it) should now work properly on Android and the browser, even when using a pen.

Qt has been patched to allow using the layer list sensibly in the face of this. Previously, it was very difficult to move layers around, since it would always interpret it as an attempt to scroll the list unless you made a weird sideways gesture to yank the layer outside of the list. Now the left side of the layer list lets you move layers and the right side lets you scroll, which is probably what you would intuitively expect from poking at those areas.

This is part of Drawpile 2.2.2.

## Minor Additions and Bugfixes

Exported brushes with identical names now get deduplicated. This was reported by Blozzom [on Discord](https://drawpile.net/discord/){:target="_blank"}. This still made it into 2.2.2.

Sound playback is now disabled under 32 bit Windows. It used to crash on some systems before, then after switching to a different interface it crashed on all systems instead. Now it's off altogether because making a bloop is not worth bringing the entire program down. This was reported by pachuco [on GitHub](https://github.com/drawpile/Drawpile/issues/1464){:target="_blank"} and got kinda last-minute hotfixed into 2.2.2.

The annotation tool now clears the text when you deselect an annotation, rather than leaving it in there and making it seem like you can still type stuff. This didn't make it into 2.2.2.

The server now properly ranks user suitability for resets down when they have worse ping, an unsuitable OS or their connection quality set to poor. It didn't sort them properly if those clients were the first to respond. This was reported by Blozzom [on Discord](https://drawpile.net/discord/){:target="_blank"} and didn't make it into 2.2.2.
