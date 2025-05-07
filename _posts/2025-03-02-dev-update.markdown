---
layout: drawpile_post
title: "Dev Update: Week 8 and 9 of 2025"
date: 2025-03-02 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks were mostly spent on making a lot of fixes and smaller features, getting ready for the release of 2.2.2. No major issues were found so far, so the final round of translations will open soon to get it ready for the final release.

## Invite Codes

There's a new "Codes" tab in the invite dialog that lets you generate limited-use codes that let someone join without entering the session password. You can recognize that an invite link uses a code if it contains a `:` in its path.

Someone joining this way also bypasses other session restrictions: they can join via web browser even if the session is set to not allow them, they don't need a registered account even if the session is set to require them and they can join even if "block new joins" is enabled in the session settings. The same person joining multiple times will usually only use the code once if the server can tell that it's the same person.

This way, you can have a public session with the above restrictions enabled, but make an exception for people you know by inviting them directly. Previously, you had to temporarily disable one of those restrictions, let the person join and then re-enable it. And if they got disconnected, you had to do it again. Now you just give them an invite code and are done.

Invite codes can be revoked later. So I guess you can also use them to let someone join without giving them the session password, or post the link publicly and if it gets abused revoke it without having to change the password for everybody.

You can also set invite codes to automatically give someone a role. So if you're one of the people that just gives everyone Operator or have a session where you need to be Trusted to do anything, you can use invite codes to pre-clear them.

A session can have up to 50 active invite codes, each with up to 50 uses. That seems like it should be enough for what they're intended to be used for, but maybe if other uses come up, we can expand upon them.

Server owners and moderators can always manage invite codes. They can also toggle whether session operators are allowed to manage them per-session. Additionally, server owners can toggle whether this is enabled by default or not.

This was suggested by Bluestrings and Liz.

![Invite codes dialog]({{ "/assets/img/2025-03-02_invitecodes.webp" | relative_url }})

## Browsering

Several web-browser-related things have been fixed and added.

You can now make Drawpile in the browser fill the screen via View → Full Screen. The default shortcut for this is Alt+Enter. It's not F11, because that's already used by the browser itself. This was suggested by Windy directly.

Keyboard input should now work on devices with both a keyboard and a touchscreen. Qt's handling of this is really weird and the way that different systems deal with virtual keyboards and input fields is even weirder. This was reported by Greendyno [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The browser could sometimes hang when you reconnected to a session or failed to connect to one. Leaving the main window in a state where you couldn't interact with it. This should be fixed now.

In the server, hosting from the browser is now a separate permission. Previously it was kind of a confusing combination of being allowed to join via browser and being allowed to change the browser allowance in the session settings. This should also no longer ask you to log in to host a session only to then tell you that you can't do it anyway. This was suggested by Bluestrings directly.

When you remove a password from a session and the server only allows browser users to join passworded sessions, any browser users that didn't join via invite code are kicked out now.

## Cintiq Positioning

The Windows Ink driver now uses a different means of determining the tablet pen position, by asking Windows to convert it rather than manually doing the math. This should fix positioning issues on Cintiq tablets when they're not set to be your primary monitor and you have UI scaling enabled. In case this causes unexpected problems in other cases, the old positioning is still available as "Windows Ink Non-Native".

The driver is originally from Krita, but they said they haven't been using it for years, instead patching Qt directly. They've deleted the unused drivers from their source tree and I deleted the "KisTablet" prefixes from the tablet drivers menu, since what little meaning they had is kinda gone now.

## Minor Additions and Bugfixes

Overly large selections no longer show the blue selection mask for performance reasons, they instead show you the marching ants outline at a reduced resolution. This was reported by Meru, MorrowShore and Shane [on Discord](https://drawpile.net/discord/){:target="_blank"}.

You can now toggle the color history swatches on the various color dialogs through the top-left hamburger menu. This is primarily useful if you have multiple of these docks visible simultaneously and don't want to have multiple copies of that swatch. Or if you have a general disdain for it and want it gone altogether I guess. This was suggested by Stickly [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The pan, zoom, inspector and color picker tools no longer get locked during catchup or a session reset. They don't modify the canvas, so there's no reason to lock you out of using them. This was reported by Greendyno [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The setting to temporarily switch a tool by pressing and holding a key now mentions that only the primary keyboard shortcut will do it, the alternate shortcut won't. This was really a bug, but it's pretty useful to have it both ways, so it's now a feature. This was reported by 3rd\_EFNO [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Creating or duplicating a layer now retains leading zeroes and spaces (or lack thereof) from the previous layer. So, for example, if you duplicate `frame001`, it now properly gives you `frame002`, rather than `frame 2`. This was reported by Greendyno [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The Windows installer now uses proper Drawpile logos instead of those ugly default red discs that made it look like you downloaded the wrong thing. This was reported by evilTriangle [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Weird letter spacing issues on Windows in the last beta have been corrected. This was caused by [Qt forgetting to apply a fix](https://bugreports.qt.io/browse/QTBUG-120554?focusedId=767021&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#comment-767021){:target="_blank"} and fixed by just pulling that in.

Generating reset images has been made faster and the resulting images are now a bit smaller. These images are generated whenever the canvas needs to be sent over the network, e.g. when it's compressed, a session reset happens, you host a session or someone joins the builtin server. The builtin server also now generates these in the background when someone joins, rather than hanging the entire application and potentially taking so long that it causes a timeout. This was reported by mukihyena [on GitHub](https://github.com/drawpile/Drawpile/issues/1443){:target="_blank"}.

The application should no longer crash when exiting full screen on macOS. It was caused by Drawpile fixing up the dock sizes during the resize, which Qt didn't like. This was reported by Axocrat [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Drawpile being forced out of fullscreen mode no longer desynchronizes the checkbox on the menu item. When you close Drawpile while in fullscreen mode, the window will reopen maximized, rather than at some dinky size. This was reported by Sinamer [on Discord](https://drawpile.net/discord/){:target="_blank"}.

On systems where it's possible to tell whether a window is maximized, un-maximizing the window will no longer mess up your dock sizes. This was reported by Meiren [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Having no layer selected now shows a forbidden cursor and a message about it in the top-right corner, rather than leaving you wondering why you can't draw anything.

Permission tiers in the layer permissions menu are now displayed properly with a dot on the currently selected tier again. This was reported by Meiren [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The software renderer no longer shows the canvas when it's supposed to be hidden, like during catchup. All other renderers already behaved this way.

Username lists in session listings have been removed. You could never see them anywhere anyway, they were just kinda a weird thing in the API and had several settings associated with them to turn them off, so it's pretty unlikely anyone will miss them.

The ability to copy a "direct link" from the invite dialog has been removed. It was pointless to make that distinction, since you can just paste the web link into Drawpile's join dialog and it'll translate it to a `drawpile://` link for you, so there's no need to copy different links.
