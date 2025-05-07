---
layout: drawpile_post
title: "Dev Update: Week 2 and 3 of 2025"
date: 2025-01-20 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks were mostly spent on reworking the host dialog, which is now in a usable state.

## Host Page Rework

The main thing that's been worked on these weeks has been the host page. This page has caused some troubles in the past due to being confusing and not having the right defaults for users that don't know their way around Drawpile. It was also kind of annoying to use for more experienced users, since it didn't have many settings you could configure here, so you had to host the session and then edit the session settings afterward to finish actually setting it up.

The page is now split up into multiple tabs, mirroring how the session settings work. The plan is to unify the host page and session settings so that they look the same in the future, since it really makes sense that you can set the same stuff the same way in both.

### Session Tab

This tab is really the only only you need if you just want to quickly host a session. It is now set up for what most new users will want to do: host a passworded session on the public server and not worry about what to title it or whatever.

The split between public and passworded sessions is now made more clear by having a drop-down selection for it, defaulting to passworded. This is called "personal" since "passworded" isn't a real word. Calling it "private" would also be an option, but I think that would give false impressions, since the session listing is still visible publicly and the server owners can still administer the session.

NSFM-ness and chat history can also be set here. It would be nice to let you make a session persistent here, but unfortunately the dialog can't tell in advance if the server even allows you to do that, which may even depend on who you log in as. So this is something to solve in the future, it's a bit of a more elaborate issue.

The server option lets you pick between `pub.drawpile.net`, entering an address or a builtin server. This will probably be expanded in the future to integrate with list servers somehow, letting you add community and other servers for hosting on and giving them that space below for information about their rules and such.

![Session tab of the host dialog]({{ "/assets/img/2025-01-20_hostsession.webp" | relative_url }})

### Listing Tab

This tab is for setting up the publicly-visible information about a session. The session title lives here now and for personal sessions it defaults to being set automatically, since the title really doesn't matter if you're just going to be sharing an invite link. This also avoids the relatively common issue of people putting something offensive into the title because they don't care and aren't aware that it's publicly visible, which is an unnecessary hurdle. If you care about your session title, you can just uncheck this box and enter one yourself. Public sessions are always required to enter a title manually.

The ID alias and announcements work as before, except that you can now add more than one announcement. That you could only add one before was basically just for space reasons.

There's plans to also add a "description" field here and some way to add tags to a session, since public sessions could really benefit from that. Currently, they have to cram all public information into the title, which isn't very nice.

![Listing tab of the host dialog]({{ "/assets/img/2025-01-20_hostlisting.webp" | relative_url }})

### Permissions Tab

Here you can change the undo limit for the session and set up feature permissions. The buttons with icons should give a quicker view of who's allowed to do what than just having the dropdowns with words in it. When you open up the dropdowns, they also show these icons.

![Permissions tab of the host dialog]({{ "/assets/img/2025-01-20_hostpermissions.webp" | relative_url }})

### Roles Tab

This tab lets you set an operator password and shows you a list of roles you loaded or imported, along with checkboxes at the bottom to change these permissions.

![Roles tab of the host dialog]({{ "/assets/img/2025-01-20_hostroles.webp" | relative_url }})

### Bans Tab

Bans are encrypted using a server-specific key, so you can't actually view them while not connected to a server. But you can load or import them and this tab will show you the bans you'll be importing once you host the session.

![Bans tab of the host dialog]({{ "/assets/img/2025-01-20_hostbans.webp" | relative_url }})

### Save/Load

The button at the bottom gives you the options to reset, save and load settings. The latter two basically work how saving and loading permissions worked before, except across all settings. You can also import and export settings from and to files. If you already have roles or bans exported from the session settins dialog, the import supports those as well.

## Android Cutoff

On some Android devices, there have been issues with the user interface getting cut off on the sides or at the top and bottom. This had stumped me for a while, since no emulator or device I tried exhibited these issues.

I finally managed to find one though and the issue was easily fixed by raising the limits for switching to the small-screen interface mode and telling dialogs to maximize themselves instead of setting them to be full-screen, since inexplicably the latter causes them to spill out of the screen on some devices.

This was reported by Anonymous, Bluestrings and Molderche.

## Minor Additions and Bugfixes

The default undo limit is now 60 instead of 30.

The rectangle and lasso selection tools now show different cursors depending on what mode they're in. For example, holding the constraint key (shift by default) will show a plus icon on the cursor to indicate that it'll unify the selection.

Layer and group view modes now always render in Normal mode, since other blend modes don't really make that much sense when you view them in isolation.

On Windows, sound playback has been rewritten to use an older, more compatible interface. Previously it used Qt's media playback, which used a newer interface that could cause crashes on some Windows 7 systems. Since Drawpile just wants to play some bloops for chat messages and such, the old interface does the job fine anyway. This was reported by anonymous, with help to figure out the cause by MorrowShore.

Canvas rotation, flip and mirror is now taken into account with regards to pen tilt inputs. This was reported by t3hm00kz [on GitHub](https://github.com/drawpile/Drawpile/issues/1423){:target="_blank"}.

Changing layer properties now properly puts down an undo point, letting you undo them without also undoing whatever happened before them. This was reported by Maffi [on Discord](https://drawpile.net/discord/){:target="_blank"}.
