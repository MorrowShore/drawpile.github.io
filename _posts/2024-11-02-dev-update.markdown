---
layout: drawpile_post
title: "Dev Update: Week 44 of 2024"
date: 2024-11-02 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last week has been spent cleaning up stuff for the next beta release. Unless any issues are found, the current state will become that beta release.

## Fill Optimization

The fill and magic wand tools have been optimized to no longer do a bunch of image merging, flooding and gap closing work in advance. Instead, they now only do that work when they actually need to fill an affected area. Depending on what you're doing, this can make a huge difference.

This means the size limit no longer affects performance like it did before. With that and fills previewing locally first, the size limit now defaults to being unlimited (or the selection area, if you have a selection active.) Since it's useful for some workflows, like doing multi-step fills to avoid having to search for leaks all across your drawng, the size limit is still present as an option though.

The video below shows a particularly egregious case, with a very large canvas that has many layers and the size limited set to unlimited. Previously, the fill would merge and gap fill across the entire canvas, which took ages. The new way only does it for that tiny area that actually gets filled, which is virtually instantaneous.

<video controls>
  <source src="{{ "/assets/vid/2024-11-02_fillopt.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Fill Tolerance Adjustment

You can now hold down the fill and magic wand tools and move the cursor left and right to adjust the tolerance for this fill/selection. This works similar to how it does in GIMP's magic wand tool. For some reason they don't support it in their fill tool, which instead does… other things when you drag it, but Drawpile makes both of those work.

This was suggested by MorrowShore [on GitHub](https://github.com/drawpile/Drawpile/issues/1327){:target="_blank"}.

The video below shows it for the magic wand tool, the fill tool behaves the same.

<video controls>
  <source src="{{ "/assets/vid/2024-11-02_wandtolerance.mp4" | relative_url }}" type="video/mp4"/>
</video>

## Persistence Permission

The server now always allows making sessions persistent via the admin API. This fixes a long-standing problem with the admin web UI where it would let you check the "persists without users" checkbox, but it would always revert back if you didn't enable the global persistence setting.

Moderators and users with the new `PERSIST` flag can also always persist sessions now, if they're using a new enough client. This allows you to have a server where persistent sessions are disabled in general, but it can be enabled for specific sessions and blessed users anyway.

The setting name in the web admin UI has been changed to say "allow anyone to make sessions persist without users", since that's what it does now. That's available in the web admin UI [dpwebadmin 0.7.0](https://github.com/drawpile/dpwebadmin/releases/tag/0.7.0).

This was suggested by Bluestrings.

## Lingering Existence

You can now configure a linger time for empty sessions in the server. This will delay the termination of non-persistent sessions when they have no users, to give people a chance to reconnect if they were the only person in a session, rather than having it instantly obliterated.

You'll probably want to pick a value for this that is long enough to give people this chance, but not so long that they'll see it still existing a long time afterwards, especially if they just wanted to test something briefly.

This is also something you'll want the web admin UI [dpwebadmin 0.7.0](https://github.com/drawpile/dpwebadmin/releases/tag/0.7.0) for.

## Prettier Protocol Errors

Setting a minimum protocol version of dp:4.24.0 (Drawpile 2.2) or later in the server will now immediately disconnect clients on an older version and tell them that they gotta update instead of leaving them sitting at a session list where all the sessions are grayed-out and have question marks next to them. It's a bit of a hack, but the server can tell that a client is outdated because Drawpile 2.2 always sends a "lookup" command before prompting you to log in, whereas older clients don't do that.

If your server only allows joining session via a direct link, users will also now be told more clearly that they need to update if they try to connect with an older client. Previously it would give a bit of an overly techy message about your client not supporting lookups, but didn't tell you how to fix it.

## Minor Additions and Bugfixes

Keyboard canvas shortcuts now properly apply even when there's a mouse button canvas shortcut with the same keys. This was reported by incoheart [on GitHub](https://github.com/drawpile/Drawpile/issues/1393){:target="_blank"}.

Pressing modifier keys and then letting go of the mouse button when adjusting transforms, selections and shapes now actually applies the modifiers you pressed, rather than forcing you to move the cursor a little to get them to register.

You can now get and create session listings via the admin API. This was [contributed by Meru](https://github.com/drawpile/Drawpile/pull/1397){:target="_blank"}.
