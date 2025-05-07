---
layout: drawpile_post
title: "Dev Update: Week 32 and 33 of 2024"
date: 2024-08-18 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

Drawpile 2.2.2-beta.3 has been released now. You can read more about that [in the release announcement on drawpile.net](https://drawpile.net/news/release-2.2.2-beta.3/){:target="_blank"}. Since then, there's been much testing and some smaller additions.

If you want to try those out, they are [in the continuous development release](https://github.com/drawpile/Drawpile/releases/tag/continuous){:target="_blank"}.

## Animation Export Scaling

The animation export dialog can now scale the video, animated image or image series to a different size, either smoothly or with nearest-neighbor interpolation. You can use this, for example, to scale down large canvases to a size that doesn't make phones explode trying to view them in full resolution or scale up pixel animations to a size that's easier to view.

This was sort of reported by Hopfel, who animated across an entire 8K canvas.

![Animation export scaling]({{ "/assets/img/2024-08-18_animscaling.webp" | relative_url }})

## Proxy Handling

Drawpile will now automatically ignore system proxies that can't actually be used to proxy its connections. This can happen if you have some kind of HTTP proxy set up on your system for example, which Qt will pick up on, but then fail any non-HTTP connections with it. Now it disables them if they would cause these kinds of errors.

To go along with this, you can also now manually disable any system proxy in the network preferences. The system info dialog under Tools → Developer Tools → System Information will also now show information about the system proxy configuration as Drawpile sees it.

Additionally, the error messages for failed connections have been made more detailed. They now tell you the internal error code and, if a proxy caused the connection to fail, will inform you about how to disable it.

This was reported by FishEggsThe [on GitHub](https://github.com/drawpile/Drawpile/issues/1360){:target="_blank"}.

## Minor Additions and Bugfixes

The margins on selections' marching ants outline and selection masks are now calculated correctly according to the current zoom level. Previously, they could cause rendering glitches on some systems. This was reported by xxxx directly.

The [help article on getting crash dumps](/help/tech/crashdumps){:target="_blank"} now tells you how it works on macOS. This was contribted by Axocrat.
