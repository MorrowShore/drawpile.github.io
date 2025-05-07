---
layout: drawpile_post
title: "Dev Update: Week 49 and 50 of 2024"
date: 2024-12-15 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks have been spent mostly on rejigging the way the host dialog and session sesttings work, but there's still a ways to go with those. But there's also two new features, one for the client and one for the server.

## Reference Dock

A new reference image dock has been implemented. This dock allows opening images and picking colors from them, which is mostly useful on systems that don't let you pick colors from the screen, like Android, the browser and Windows 11 (10 still allows it.)

You can enable the dock via View → Docks → Reference. It allows you to open a file or paste an image through the menu at the top left, which you can then move, zoom and rotate. Clicking onto the image picks a color and middle click, shift+left click or Ctrl+left click pans. You can switch between pick and pan via the buttons at the top as well, which is mostly useful on devices without a middle click or a keyboard.

An obvious thing still missing is how to handle persistence. Currently, the dock only lets you open one file at a time and will "forget" what you had open after closing Drawpile. I thought about adding tabs to view multiple images and/or having a history of previously-opened images available, but I'm not totally sure on how that should be designed. If you have ideas for it, [give your feedback on it on Discord, IRC, Matrix or GitHub](https://drawpile.net/help/){:target="_blank"}!

This was kind of suggested in a different way by leandro2222 [on GitHub](https://github.com/drawpile/Drawpile/issues/1147){:target="_blank"}, the impetus for implementing it as a dock came from 3rd_EFNO [on Discord](https://drawpile.net/discord/){:target="_blank"}.

![Reference image dock]({{ "/assets/img/2024-12-15_refdock.webp" | relative_url }})

## Admin Chat

The admin API and server web admin interface now support two-way chat. You can connect to the chat of a session by sending an initial message and then read the responses until you disconnect from the chat again.

Previously server owners could only send messages one way, so if they wanted to actually talk to people in a session, they had to join it to do so. Having this two-way chat means they now have an easier and less invasive way of doing it.

## Minor Additions and Bugfixes

Color dialogs now always use the HSV color space, since the other ones don't actually work properly. This was reported by Meru [on Discord](https://drawpile.net/discord/){:target="_blank"}.

When a fill is pending, it will now get applied when you manipulate the layer list. Previously it would exhibit some strange behaviors, causing the preview to disappear and then the fill getting applied to a wrong layer.
