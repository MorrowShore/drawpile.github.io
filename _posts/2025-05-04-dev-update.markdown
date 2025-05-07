---
layout: drawpile_post
title: "Dev Update: Week 18 of 2025"
date: 2025-05-04 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last week was spent on more Drawpile 2.3 features. The protocol is approaching a stable state, once that is reached, testing can commence.

## Drawing Inside Selections

Selections will now mask brush strokes, like you would expect them to. The way that this works under the hood is that the brush engine will upload only the parts of your selection that are relevant for masking your stroke as you make it and then flag the drawing commands with a reference to that selection. Those selection synchronization messages don't participate in undo, so once a part of a selection is synchronized, it will stay that way. Otherwise, selections continue to be local-only, since they don't affect anything visible to others.

The same kind of technique could be used e.g. for brush tip images in the future, since that's a similar case of permanently synchronizing on demand.

<video controls>
  <source src="{{ "/assets/vid/2025-05-04_selectionmask.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Layer Alpha Lock

There's now an alpha lock toggle for layers. This works the same as alpha locking your tool ("Recolor mode"), except you set it on the layer. This only affects you, not anybody else drawing on the same layer.

This feature doesn't add any new functionality and I don't think it has any advantages over setting it on your tool. It's just there to be brain-compatible with people coming from other drawing programs that handle alpha locking this way, who would previously despair looking for the option in the wrong place.

<video controls>
  <source src="{{ "/assets/vid/2025-05-04_layeralphalock.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Layer Count Limits

Session operators can now set a per-user layer count limit in the session settings. The default is 1000 for everyone except operators, who get unlimited layers. Attempting to create more layers than you're able to now shows an error, rather than just doing nothing.

Like with brush sizes, you can currently only set the limit for everyone except operators and it's only in the session settings dialog, not in the host dialog yet. This is just a shortcoming of the UI, internally you can set the limit for each user tier separately and there's nothing that prevents setting it when you host the session.

## Faster Compression

Images and masks sent over the network are now sent in a channel-separated, delta-encoded, zstd-compressed format. This is orders of magnitude faster than the old deflate compression, with the resulting sizes being equivalent. This compression is also part of what makes the new file format (written about in the blog entries of [Week 10 and 11](/devblog/2025/03/15/dev-update.html#project-file-format) and [Week 12](/devblog/2025/03/29/dev-update.html#canvas-file-format)) 3000% faster. Thanks goes to Bonbli for suggesting splitting the channels and delta-encoding them for a smaller size.

The old compression is still supported for the yet to be implemented 2.2 compatibility mode, but 2.3 clients won't generate commands with them anymore.

## Minor Additions and Bugfixes

Switching layers after doing a fill will no longer apply the fill to the wrong layer. This was reported by greendyno [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Disabling sketch mode on a layer that had 0% opacity no longer sets its opacity to 1%. This was reported by Meru [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Deleting a hidden layer and then subsequently creating a layer with the same ID when your ping is somewhat high no longer ends up with the layer gettin stuck in a hidden state until you select a different layer. This was reported by MachKerman [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Drawpile 2.3 now includes the target opacity and blend mode in its transform commands, so it no longer requires permissions to cut and paste the image to get that effect.
