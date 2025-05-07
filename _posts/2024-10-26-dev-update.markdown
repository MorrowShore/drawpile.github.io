---
layout: drawpile_post
title: "Dev Update: Week 42 and 43 of 2024"
date: 2024-10-26 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks have been pretty busy development-wise, cleaning up things and getting ready for the release of the next beta.

The translation files have been updated, so if you want to translate things to your language, you can do so [on Weblate](https://hosted.weblate.org/engage/drawpile/){:target="_blank"}.

## Path to Signing

Drawpile has now been provided with free code signing for the Windows client by [SignPath.io](https://about.signpath.io/){:target="_blank"}, certificate by [SignPath Foundation](https://signpath.org/){:target="_blank"}. That means that the next beta release should no longer spring up SmartScreen warnings that you have to click through, without us having to pay several hundred Euros a year for it. Needless to say, this is an excellent offering for open source and public software, which none of the other code signing companies seem to even think about.

Unlike many other solutions in that space, where you have to manually boot into Windows, plug in a magic USB stick they sent you per mail and sign the binaries by hand, SignPath also integrates with our CI workflow on GitHub Actions. So they got my recommendation on that side as well.

## Retool Fill Tool

The fill tool has changed its mode of operation a bit again. It still previews fills locally to avoid "flashbanging" other people, but it no longer requires a second click to confirm the fill by default. Instead you can just keep filling and if you have a spill you can undo it (or hit the escape key or click the cancel button) to get rid of it without disrupting anyone else. This makes the tool mostly behave like it does in single-user programs on the surface, but still avoids causing a mess across the canvas.

The old mode of operation is still available though, since being able to edit your fills was a very useful feature and confirming via clicking is nice if you don't have a keyboard available. It's just not the default, since is so different from what other software does.

The magic wand now always selects immediately, since the only reason it didn't do so before was to match up with how the fill tool worked. Selections are local-only anyway, so it doesn't have problems with causing disruptive spills.

![Fill tool menu]({{ "/assets/img/2024-10-26_fillretool.webp" | relative_url }})

## More Brush Shortcuts

There's a few more brush-related shortcuts now for further click-less operation thereof:

* Next brush and previous brush. These will move the current selection in the brushes dock.
* Next tag and previous tag. These will change the current tag in the brushes dock.
* Next slot and previous slot. These move through your brush slots one step at a time.

Next and previous brush are are bound to <kbd>.</kbd> (period) and <kbd>,</kbd> (comma) by default, the rest are unbound. You can change that in the preferences of course.

## Better GIF Export

We now use ffmpeg's libraries to export GIFs. They are already used for video exports, so doing this allowed throwing out a bunch of extra code.

The export is also faster now, generates much better palettes and allows for transparent backgrounds. That is, as far as GIF goes anyway, the format does not support partial transparency.

## Issues of Scale

UI scaling is used on displays with large resolutions, 2K and above, where otherwise the interface would be too tiny to make out. This was implemented already, but had some strange behavior where Qt would inexplicably round scaling values to multiples of 100%. Which means if you used the very common scaling of 150%, you'd instead get a 200% scaled interface, making it way too ginormous to use. That's fixed now, the values don't get rounded anymore.

There was also issues where trying to override the scale factor wouldn't actually do so, it would instead multiply the chosen factor into the one of the screen, which is another baffling behavior on Qt's part. That's also fixed now, picking a custom scale factor overrides the scaling now, as it should.

Scaling is enabled by default on most operating systems, the exception being Android because doing so just seems to break things. You can change these settings under Edit → Preferences, in the User Interface category.

I also had letting you change the scaling without restarting the application working on Linux, but unfortunately other platforms don't like this at all, so I had to throw it out again. Alas.

This was reported by blau, Buch, Chryssabliss and ShotgunnerFox in various places.

## Matrices

Matrix is a decentralized instant messenger. It's something like Discord, but not run by a single company.

There's now a Matrix room for Drawpile, which you can find at <https://drawpile.net/matrix/>. It gets relayed over to the Discord server as well, so interaction between those two realms is possible.

Thanks to Tom the Dragon for setting this up and providing the bridge.

## Less Annotation Mess

Annotations (or "text boxes") now delete themselves if you click away from them while they are empty. This avoids the all too common mess of having empty text boxes strewn about the canvas where someone just wanted to click away from one of their text boxes or something. If you did want that empty text box, you can hit undo and it will return. This was suggested by [MorrowShore on GitHub](https://github.com/drawpile/Drawpile/issues/1351){:target="_blank"}.

Similarly, clicking off of an annotation will now only deselect it on the first click, rather than immediately create a new annotation in the spot you clicked. This should prevent the creation of those nonsense annotations in the first place in many cases.

Also, as a very minor thing, Edit → Delete Empty Annotations can now be undone without also undoing the thing that happened before it.

## Permission Clarification

When you don't have the permission to do something, Drawpile now shows a clear message about it in many cases. Previously, they would usually just disable the action or tool that actuated them, which was pretty confusing because things would seemingly just "not work" for no explicable reason.

Undo, redo, filling/recoloring/color erasing/clearing a selection, cutting and pasting now show a message about which permission you are missing if you're not allowed to perform them.

If you try to apply a transform that you're not allowed to apply, you will get a message to that effect and the transform won't be applied. If you don't have any transform permission Drawpile will also refuse to start the transform at all, but certain transforms need to do a cut and paste internally, in which so you need those permissions for them.

The fill tool now shows an appropriate message in its settings when you don't have permission to use it and it will say "tool is locked" in the corner. Previously, it wouldn't even let you select the tool, which was pretty confusing.

The laser pointer also behaves like this when you don't have permission to use it, but also when you disabled laser trails in the View menu. In the latter case, it will prompt you to re-enable it.

Finally, the annotation tool will also tell you if you have annotations disabled in the View menu and prompt you to re-enable them. If you don't have permission to create annotations, it will tell you about that when you attempt to create one, but you can still edit them as normal.

Issues in this direction where reported by [Venesio on GitHub](https://github.com/drawpile/Drawpile/issues/1197){:target="_blank"}.

## Expansion Prompt

When you activate one of the expand up/down/left/right actions, they will no longer immediately resize the canvas in that direction. Instead, the resize canvas dialog will open. However, the shortcuts continue to work in this dialog, so you can keep hitting the same button as before, you just have to confirm it by hitting Enter at the end. You can also hold down those shortcuts now, which will make them auto-repeat and keep expanding in that direction.

Doing it this way has significant performance advantages. Resizing the canvas is a pretty slow and expensive operation, so doing it all in one step is much faster than doing a bunch of individual resize operations. Prompting you to confirm the resize also prevents the all too common accidental resize, where someone accidentally mashed one of the four shortcuts along the way and expanded the canvas in some random direction, messing up some art piece in a corner on accident.

Parts of this were suggested by [tobiasBora on GitHub](https://github.com/drawpile/Drawpile/issues/901){:target="_blank"}.

## Minor Additions and Bugfixes

There's now [installation instructions for macOS Sequoia](/help/tech/installation){:target="_blank"}, because Apple made that much more difficult. This was contributed by Axocrat.

Android uses the OpenGL hardware renderer by default now. This is faster and shouldn't cause additional driver issues or whatever, since OpenGL is being used to render the entire interface on this platform anyway.

Drawpile will no longer use Android system message boxes, because their behavior is just all kinds of broken (you might have seen a box with three "Yes" buttons for example.) It now uses Qt's message boxes, like other platforms do. This was reported by Hopfel directly.

Exporting animations that encompass the whole canvas no longer get pointlessly scaled by a single pixel in each dimension.

Transforms now keep their aspect ratio when you scale them from the corner while holding the constrain tool key (Shift by default.) Previously it would get messed up by where you clicked on the corner. This was reported by Blozzom [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The server now supports integrating with systemd's watchdog. The service file sets the `WatchdogSec=60s` and `Restart=always`, so that if the server gets stuck in a non-responsive state for longer than one minute, it gets restarted. You can configure this stuff however you want of course.
