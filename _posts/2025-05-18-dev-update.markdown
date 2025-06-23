---
layout: drawpile_post
title: "Dev Update: Week 19 and 20 of 2025"
date: 2025-05-18 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks were spent on getting Drawpile 2.3 ready for use with a compatibility mode for 2.2 sessions, with some extra features added.

## Mainlining Drawpile 2.3

Drawpile 2.3 is now in the mainline. That means that you can download it in the alpha release <https://github.com/drawpile/Drawpile/releases/tag/continuous>

You can generally use both versions side by side. For example, on Windows you can use the portable version, on Linux you can just keep both AppImages around. On Android you need something like Shelter to do a side-by-side installation.

It's backward-compatible with Drawpile 2.2, so you can join sessions hosted with that version. Most of the new features won't be available of course, but some of them are available, like the new gradient tool or alpha locking of layers.

## DPCS File Format

There's a new DPCS canvas save file format available, called "Drawpile Canvas (.dpcs)" in the file picker. This is the canvas format part of the new project file format [talked about in a previous post](https://docs.drawpile.net/devblog/2025/03/15/dev-update#project-file-format){:target="_blank"}, the rest of the project format will follow in the future.

The format is *much* faster than ORA and usually also produces files that have a smaller size. It often even saves faster than it takes to export the canvas as a JPEG file.

Anecdotally, I have a very large canvas that takes around 2 minutes to save as an ORA file and 11 seconds to export as a JPEG. Saving it to a DPCS file only takes 2 seconds. Opening the file is also faster, it takes around 4 seconds for ORA, but only 1.5 seconds for DPCS. Drawpile now shows the time it took to save and load images in the status bar (in the bottom-left corner, where the cursor coordinates are normally shown), if you want to compare the times yourself.

Drawpile still defaults to the ORA format for now, but you can configure your preferred formats under Edit → Preferences, in the new File tab. You can also switch your preferred export format here, which previously always defaulted to PNG.

![Preferred file format settings with DPCS selected]({{ "/assets/img/2025-05-18_dpcs.webp" | relative_url }})

## Gradient Tool

There's now a new gradient tool, which lets you make linear and radial gradients. To avoid your gradient spilling across the entire canvas and disrupting other people, it only operates inside of a selection.

Gradients are editable and won't become visible to other people until you apply them. You can adjust the position, opacity, blend mode, alpha preserve, spread, color etc. before applying it by either clicking the apply button, hitting enter, switching tools or clicking somewhere outside the gradient line.

Currently there's only foreground to transparency and foreground to background gradients (and vice-versa), but presumably a custom gradient option will also be added. It's technically possible, just needs the user interface figured out for it.

This was suggested by ChristianJohnsten [on GitHub](https://github.com/drawpile/Drawpile/issues/293){:target="_blank"}. The tool also works in 2.2 sessions, since it just pastes the resulting image.

<video controls>
  <source src="{{ "/assets/vid/2025-05-18_gradient.mp4" | relative_url }}" type="video/mp4"/>
</video>

## More Layer Locks

In addition to locking the contents of a layer, you can also now lock its properties (like opacity or blend mode) and/or its position in the layer tree.

These lock actions are also now available in the Layer menu at the top of the window and in the layer context menu. You can assign keyboard shortcuts to them now as well.

Position locking was suggested by Athena [on Discord](https://drawpile.net/discord/){:target="_blank"}. It's not available in 2.2 sessions.

![Layer locks]({{ "/assets/img/2025-05-18_layerlocks.webp" | relative_url }})

## Long Long Canvas

Drawpile now allows canvases of up to one million pixels in a single direction, as long as the total number of pixels doesn't exceed 1,073,676,289, which is 32767 squared. Previously it only allowed up to 32767 pixels in either direction, so the total maximum size didn't change, but you can now make your canvases very long and narrow or very wide and short.

Note that this size exceeds the limits of many formats: JPEG files can only handle 65,535 pixels in either dimension, GIF 65,536, PSD 30,000 and WEBP 16,384. DPCS, ORA and PNG are fine with any size though.

This was suggested by johannesCmayer [on GitHub](https://github.com/drawpile/Drawpile/issues/1193){:target="_blank"} and is not available in 2.2 sessions.

## Classic Jitter

The classic Drawpile brushes (soft, round pixel and square pixel) now have a Jitter setting. This will randomly offset the brush dabs from the center of the stroke, letting you make pencil-, charcoal- or spraycan-esque brushes.

The setting is available in the brush settings dialog, available by clicking on the brush preview, under Edit → Brush Settings or by hitting F7 by default. This works in 2.2 sessions.

![Jitter setting]({{ "/assets/img/2025-05-18_jitter.webp" | relative_url }})

## …and Deselect

There's now a set of new shortcuts that let you switch tools and deselect in the same action. This may help cut out an extra action of deselecting when switching back to the brush, similar to how it worked in previous versions of Drawpile where selections weren't persistent and would always go away when you switched tools. I think this is preferable over some kind of global "make it work like it used to" toggle, since this way you can stil switch tools and retain the selection to draw inside of it or something.

This was suggested by Bonbli [on Discord](https://drawpile.net/discord/){:target="_blank"}.

![Tool and deselect shortcuts]({{ "/assets/img/2025-05-18_anddeselect.webp" | relative_url }})

## Minor Additions and Bugfixes

Touch pan, zoom and rotation is now smoothed by default, which should make it feel better on devices that have low touch input frequency, such as iPads in the browser. This is available in the web browser version already. You can adjust or disable the smoothing in the touch preferences. This was repoorted/suggested by Jay-Man [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Pigment mode no longer makes a grid pattern with some brushes when smudging. This was reported by username [on Discord](https://drawpile.net/discord/){:target="_blank"}.

MyPaint brushes with a fixed X and Y offset and no other offset settings or dynamics now show the outline in the place where the strokes will go. I'm told a fixed offset like that is useful for technical drawing. This was suggested by Phoneme directly.

Making selections no longer marks the canvas as needing to be saved. This was reported by MachKerman [on Discord](https://drawpile.net/discord/){:target="_blank"}.
