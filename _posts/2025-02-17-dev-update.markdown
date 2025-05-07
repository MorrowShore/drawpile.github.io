---
layout: drawpile_post
title: "Dev Update: Week 6 and 7 of 2025"
date: 2025-02-17 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks were mostly spent fixing bugs and releasing [Drawpile 2.2.2-beta.5](https://drawpile.net/news/release-2.2.2-beta.5/){:target="_blank"}. Unless blocking issues are found, this will effectively become the final version of Drawpile 2.2.2.

The beta on Flathub for Linux is updated as well. F-Droid for Android should be updated in the coming days, an update has been submitted, accepted and built already, so it's just waiting to be published.

## More Multi-Layer Operations

You can now merge and change permissions on multiple layers at once.

Merging layers will merge everything into the current layer, which is usually the last one you selected. If that layer is nested underneath a group you also selected, it will be merged into that group instead, since merging a parent into its child would be a paradox.

Changing permissions works as you'd probably expect, you can lock, change the censor state and restrict layers to certain tiers or users when you have multiple ones selected. This doesn't solve the issue of permissions not inheriting from groups to their layers automatically, but it should make it less annoying to change permissions anyway.

## Color Themes

A set of new color themes have been added, courtesy of Weenifer [on Discord](https://drawpile.net/discord/){:target="_blank"}.

If you like none of these themes, you can also [customize them yourself](/help/tech/customassets){:target="_blank"}.

![Showing off the new color themes]({{ "/assets/img/2025-02-17_themes.webp" | relative_url }})

## Going On Safari

Safari 18 has [a devastating bug](https://bugs.webkit.org/show_bug.cgi?id=284752){:target="_blank"} that breaks WebAssembly applications like Drawpile. And since all browsers on iPad are Safari under the hood, it meant that Drawpile was basically unusable if you updated to it.

The cause of this is the implementation of asynchronous code using Asyncify, which makes it so that code can be suspended in-place until something happens to make it continue. In JavaScript terms, it enables using `async` and `await` in WebAssembly. Unfortunately, this greatly increases the required amount of stack space, which is what Apple broke in their browser (again.)

There's an alternative to this called JavaScript Promise Integration (JSPI), which is much more light-weight by integrating JavaScript promises into WebAssembly. But of course Safari doesn't implement it, so that's of no use.

However, the only reason we even need asynchronous code in the first place is for something Qt calls nested event loops. This is used to, for example, show a message box to ask the user something without having to do the whole rigmarole of setting up a callback for it. That's pretty easy to work around by just writing the longer code instead.

More crucially, it's required for drag and drop to work. So without Asyncify, you can't drag layers or timeline frames around, which is kinda important. And to be extra annoying, attempting to do so will just hang the program because Qt doesn't check for this case properly.

Luckily, it turns out that this is not a fundamental implementation requirement, it's more just a happenstance of API design. So with a medium amount of patching around in the Qt internals, I managed to make drag and drop to run without requiring a nested event loop. Along with removing any other instance where nested event loops were used for convenience, this removes the requirement for asynchronous code, which means we no longer need Asyncify, which means the amount of stack space required is heavily reduced and Safari works again.

As a bonus, this also improves performance, makes the binary smaller and reduces the build times. So, while it did cause a few days of work, we'll have lasting improvements from it and not just a temporary workaround for a busted browser.

This was reported and thoroughly tested by grimsley and additionally tested by lunashadowbane [on Discord](https://drawpile.net/discord/){:target="_blank"}.

## Minor Additions and Bugfixes

The nonsensical license page has been removed from the Windows installer. Drawpile doesn't have an end-user license agreement ("EULA") you need to agree to, the only reason it showed you the goofball license page before is that it's kinda convoluted to remove. Luckily Inkscape did it already, so I was able to base the feature off of what they did.

The Fusion style is now used by default on macOS because the previously-used macOS style can crash when you use it in conjunction with Sidecar. This was reported by Axocrat [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The software renderer no longer creates errant dark lines with moving the cursor when fractional scaling is involved. This was reported by lambda20xx [on Discord](https://drawpile.net/discord/){:target="_blank"}.

You can now drag ORA, PSD and brush pack ZIP files into Drawpile's window and they will open as you would expect, rather than telling you that they can't be pasted into the canvas. This was reported by MorrowShore [on GitHub](https://github.com/drawpile/Drawpile/issues/1411){:target="_blank"}.

Laser trails are no longer rendered during catchup to a session. All that did was spam your screen with a bunch of lingering lasers anyway. This was reported by MorrowShore [on GitHub](https://github.com/drawpile/Drawpile/issues/1410){:target="_blank"}.
