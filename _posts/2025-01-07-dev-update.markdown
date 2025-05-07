---
layout: drawpile_post
title: "Dev Update: Week 51 and 52 of 2024 and 1 of 2025"
date: 2025-01-07 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last three weeks involved a lot of holidays, but there was still some development that got in here and there.

## Quick Drag

There's now a new option in the rectangular and lasso selection tools called "drag to move". This will allow you to quickly move selections and their contents, without entering into a full transform mode.

When the box is checked, dragging a selection will move its contents around. Clicking off of it will immediately apply the transform and deselect. If you click off of it and hold the button down, you can immediately start a new selection, similar to how it worked in previous Drawpile versions.

To switch from this quick move operation to a full transform, you can click on the transform, similar to how you can click on a selection. This will then behave like it did before, where a double-click outside of the transform applies it. Additionally, you can also now right-click once outside of the transform to apply.

You can also now drag the edge of a selection to move the selection itself, rather than its contents, similar to how it works in Krita and CSP.

This was suggested and tested by Blozzom, Lungy, MorrowShore, SadColor and Zheida [on Discord](https://drawpile.net/discord/){:target="_blank"}. The video below shows it off.

<video controls>
  <source src="{{ "/assets/vid/2025-01-07_quickdrag.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Templating

Session templates is a feature that's been in the dedicated server for a while. You can put recordings into a directory, tell the server to scan for them and then those sessions will always pretend to exist on the server, even when they're not actually running. When you join them, a real session will spring into existence from the template. That will run until it terminates one way or another, returning to the pretend state from before and restarting afresh again when someone joins.

Presumably no server owner has really been using this feature for a while, because, uh, it didn't really work in numerous ways. So there's been a bunch of fixes made to it to actually return it to a functional state.

When you join a template session that isn't running yet via a direct link, it will no longer balk at you that it isn't running and instead join the session as it should.

Permissions now get loaded properly in templates, before they were always left on the default. The title and settings of such sessions no longer initially appear blank. When the server is set to record sessions, the recording is now properly started for templated sessions when they are intantiated, rather than not doing so until the first reset. Web socket allowance now works properly, before it would always be disabled until the session was instantated.

When you specify an invalid password format in a session template, it will now guess that you meant to specify a plaintext password instead and warn you about it. Previously, it would just make the session unjoinable altogether because you couldn't enter the invalid password.

There's new settings for `authOnly`, to only allow registered users. [The documentation on session templates](https://docs.drawpile.net/help/server/dedicatedserver#session-templates){:target="_blank"} has been updated, expanded and corrected in some places.

The web admin interface no longer errors because of missing IDs when you view the sessions page when there's a non-instantiated template in the list. It instead properly shows them as templates. The fix is available [in the release version 0.8.1](https://github.com/drawpile/dpwebadmin/releases){:target="_blank"}.

## Minor Additions and Bugfixes

Docks now attempt to stay at their intended size when the window changes size, rather than remaining squished. This was particularly annoying on Windows tablets, where the keyboard often briefly squashes the application window, or on devices where you may accidentally rotate the device. This was reported by vipperz [on GitHub](https://github.com/drawpile/Drawpile/issues/1349){:target="_blank"}.

Clicking and dragging the magic wand and fill tool to adjust the tolerance now work at all zoom levels. Before they could get stuck until you moved the cursor at a higher speed.

The resize canvas dialog now fits itself too the active transform, if one exists. This is useful when you want to paste an external image as a background or something, you can now paste it, go into Edit → Resize Canvas and then immediately hit OK for it to fit the canvas around your floating pasted image. This was suggested by Meru [on Discord](https://drawpile.net/discord/){:target="_blank"}.

When you export a canvas to something other than ORA, it no longer marks the canvas as saved. *Saving* (File → Save (As) instead of Export) to another format still marks it saved though, since that's kinda what you asked for.

The fill and magic wand tools now operate at 8 bit precision instead of the full 15 bits of the underlying image. This avoids issues where a tolerance of 0 would get snagged on stuff that you couldn't even see on your screen or with the color picker because the differences were so infinitesimally small. This was reported by vipperz [on GitHub](https://github.com/drawpile/Drawpile/issues/1419){:target="_blank"}.

The dprectool command-line tool now has a `-p`/`--pass` option to allow you to choose what kinds of messages it should pass through. Previously, it would filter out any message not relevant for playing back in the client, which wasn't suitable for session templates because it excluded permission messages.

The drawpile-cmd and drawpile-timelapse command-line tools now support other scaling algorithms via the `-I`/`--interpolation` option. This uses ffmpeg's "fastbilinear" scaling by default now, which makes it go faster than before. A scaling option like lanczos will work better when scaling very large canvases down, where bilinear scaling doesn't give good results. Nearest-neighbor scaling is also available and may be useful for pixel art. This was caused by Saphiros.

Worked around Android devices that report invalid last touch points when dragging with two fingers. This would cause the canvas to move at way too fast a speed. This was reported by quandaledingle44 [on GitHub](https://github.com/drawpile/Drawpile/issues/1418){:target="_blank"}.

Putting two fingers down, then releasing one of them and then putting it back down again now no longer snaps the zoom and rotation back, but instead lets you continue it. This was repoted by Partack [on Discord](https://drawpile.net/discord/){:target="_blank"}.
