---
layout: drawpile_post
title: "Dev Update: Week 4 of 2025"
date: 2025-01-26 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last week saw the addition of a sketch mode for layers, some brush slot fixes, solving mysterious browser behaviors and some server-side amendments.

## Local Sketch Mode

There is now a "sketch mode" you can toggle on layers and layer groups. This lets you change the opacity and tint of a layer temporarily so that you can sketch over it. The opacity and tint can be changed per layer, the default is to set the opacity to 75% and tint it a light blue, since that's good for turning a monochrome sketch into something reasonable to line over. The tinting also works for sketches that use a white background, for example because you pasted a scanned traditional sketch or something. If you don't want the tint, you can simply set that to be transparent.

Sketch mode is local, so it's only visible to you, other people in the same session don't see it. That means you don't need special layer permissions to enable it and don't disrupt anyone else that may be drawing on the same layer by changing the opacity from under them.

This was suggested by abrasivetroop and leandro2222 [on GitHub](https://github.com/drawpile/Drawpile/issues/960){:target="_blank"}.

Here's a video showing it in action:

<video controls>
  <source src="{{ "/assets/vid/2025-01-25_sketchmode.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Onion Skin Tintage

The same tint algorithm as for the sketch mode above is also used for onion skins in frame view now. It seems to me like it gives much better visible results when onion skinning colored layers. And of course it works when importing opaque frames with white backgrounds, where before they would just turn into a monocolored plane.

## Brush Slot Amendments

When you use per-slot colors, the slots now also remember the background color you had set. It is shown as a thin line between the slot number and the foreground color on the slot buttons. Previously, only the foreground color would get remembered per slot.

When you create or edit a brush, the brushes dock now scrolls to it upon saving. Previously it could happen that the brush would get thrown somewhere that you couldn't see, which wasn't very good for feedback that it actually saved.

You can now edit brush names, descriptions and thumbnails even when not attaching slots to brushes. The next/previous brush/tag shortcuts now also work properly in that case, where previously they would only move the selection around but not actually change your current brush.

## Brower Mysteries

After chasing some strange behavior when testing out the new host dialog, I noticed that apparently the browser somehow manages to receive messages while the application is already busy handling them. This is pretty mysterious, since it all happens on the same thread, so it shouldn't really be possible. I'm chalking it up to some kind of asynchronous thing that Qt does under the hood, which then gives the WebSocket connection a chance to slide in.

Obviously functions meant to run on a single thread don't expect to be reentered like this, so this could cause all sorts of mayhem. It was fixed by making the receive handler reentrant: if it's already running, it just sets a flag and returns. The already-running handler will notice that flag when it's done and run itself again.

This fix is deployed to the official web client already. It should fix some mysterious errors during login, like getting "invalid state" or "incompatible server" messages or the application outright hanging or crashing.

## Minor Additions and Bugfixes

Annotation background colors of annotations are now saved properly. Previously there were some cases where it would save it in a way that didn't read back properly.

When you change a session's password, the server will now broadcast that change to everyone in the session so that their invite links under Session → Invite get updated to the new password. It previously would remain stale until you reconnected.

The admin API and web interface now support pagination and searching of server logs. Previously it would only show the last hundred log messages and if you wanted more you had to use journalctl or manually run a query against the SQLite database.

If you set an automatic session title via the new host page, the server is now aware of that fact. It currently only has the effect of changing the title if the session founder changes, which is only something you can do via the admin API anyway.
