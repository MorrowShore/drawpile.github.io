---
layout: drawpile_post
title: "Dev Update: Week 5 of 2025"
date: 2025-02-02 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

Last week translations for the next Drawpile beta were opened and one big feature has been implemented: selecting multiple layers to let you group them, move them, delete them etc. all at once.

If you want to help translate Drawpile to your language, you can do so [through Weblate](https://hosted.weblate.org/engage/drawpile/){:target="_blank"}!

Drawpile 2.2.2-beta.5 will probably be released next week or so. If everything goes to plan, this will be the last beta before the stable release.

## Multi-Layer Selection

You can now select multiple layers in the layer list at once, either by holding Ctrl or Shift while selecting them or by clicking on or dragging over the boxes on the right.

When you have multiple layers selected, you can move them all as one set, delete them simultaneously, clear or fill across them and change their opacity, blend mode or sketch mode all at once. When you create a layer group, it will automatically group the selected layers into it if you have multiple of them selected and it wouldn't create a paradox of nested layers.

When you start a transform, your selected layers will also become the set of initially-checked layers. Otherwise, the transform checkbox stuff still works as before, since the logic of transforms is slightly different to the general case of selecting multiple layers. There is the slight added convenience of being able to drag across the checkboxes here as well.

What isn't implemented yet is merging multiple layers at once and changing permissions on them, but that will probably happen in the future.

Drawing only happens on the "current" layer, which is the one that has the opaque highlight. Selecting multiple layers won't suddenly make you draw across multiple layers, since that's really not useful behavior.

Parts of this were suggested by MorrowShore [on GitHub](https://github.com/drawpile/Drawpile/issues/1385){:target="_blank"}.

<video controls>
  <source src="{{ "/assets/vid/2025-02-02_multilayer.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Drag Visibility Toggle

Like with layer selections, you can also now drag over the layer icons to hide multiple layers in a row, rather than having to click on each one.

This was suggested by MorrowShore [on GitHub](https://github.com/drawpile/Drawpile/issues/1385){:target="_blank"}.

<video controls>
  <source src="{{ "/assets/vid/2025-02-02_dragvisibility.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Serverage

Some more loosely related server work has been going on.

The server can now distinguish actual web browser connections from the application. It can tell this by the presence of an Origin header, since browsers always send that, but our applications don't. This means that those clients won't be restricted by browser-related permissions if they happen to connect via WebSockets. Which isn't something that currently happens unless you try really hard, but may be coming in the future, since WebSockets have many advantages with regards to the infrastructure you can use with them.

The server now allows the browser to host sessions with a password if the server is set to allow that specifically. If you try to host a public session on such a server, you will get an error message that only passworded sessions are allowed. Previously, all host attempts were rejected instead.

Lastly, the client now tells the server which hostname it used to connect to it. You can choose to restrict connections to the "correct" hostname as specified by the `--local-host` parameter. Doing so probably doesn't make sense currently though, since it means older clients won't be able to connect. This kinda thing may be expanded in the future to allow you to run multiple servers on the same machine. The Abstract community server may also end up making use of this one way or another.

## Minor Additions and Bugfixes

There's now a File → Export Again option to save your image under the same name without prompting you to pick a file. This was suggested by gerroon [on GitHub](https://github.com/drawpile/Drawpile/issues/1298){:target="_blank"}.

The flipbook crop is now saved properly when you crop multiple times without resetting it in-between. Previously that would cause the crop to get messed up when you closed and reopened the flipbook or exported your supposed cropped area. This was reported by BornIncompetence [on Discord](https://drawpile.net/discord/){:target="_blank"}.

When you set the canvas background color, the color dialog will now show a checkerboard behind the preview color when you reduce the alpha value, rather than always showing it as opaque.
