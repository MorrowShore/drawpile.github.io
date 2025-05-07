---
layout: drawpile_help
title: "Testing Releases"
description: "An explanation of Drawpile's release types and where to find them."
date: 2024-08-07 00:00:00 +0200
category: "help"
tag: help development
---

Drawpile has a few different kinds of releases: stable, beta and continuous. You can use the latter ones if you want to help Drawpile's development or get access to new features and fixes early.

The program always stays compatible with at least one previous version, either by being fully interoparable or by providing some kind of compatibility mode. So you can use these development releases and still join sessions hosted with the stable version without having to convince everybody else to switch to it.

* Table of contents
{:toc}

## Stable Releases

These are the "normal" releases of Drawpile. You can find those [in the downloads section on drawpile.net](https://drawpile.net/download/){:target="_blank"}.

The version number of stable releases that you find under Help → About Drawpile will just be three digits separated by dots. For example, `2.2.1`.

## Beta Releases

Beta releases are preliminary releases of Drawpile for public testing. They are generally also stable in the sense that they don't crash randomly, but contain new features that need some testing and feedback, so things change more. Once we're happy with a beta release, it will be promoted to a stable version.

You can find beta releases [in the "Beta" tab in the downloads section on drawpile.net](https://drawpile.net/download/#Beta){:target="_blank"}. If you don't see that tab, that means there's currently no beta release.

The version number of beta releases that you find under Help → About Drawpile always has "-beta", a dot and a number afterwards. For example, `2.2.2-beta.2`.

### Flathub Beta

On Linux, you can install the latest beta release through Flathub. To do so, you first have to add the flathub-beta repository. Open a terminal and enter the following:

```
flatpak remote-add flathub-beta https://flathub.org/beta-repo/flathub-beta.flatpakrepo
```

Afterwards, enter the following to install the latest beta version of Drawpile:

```
flatpak install flathub-beta net.drawpile.drawpile
```

Then activate it:

```
flatpak make-current net.drawpile.drawpile beta
```

Now when you run Drawpile, it should give you the beta version. Check under Help → About Drawpile to verify which version you're running.

You can go back to the main version by using `stable` instead of `beta` in the above command.

## Continuous Release

The continuous release is a build of Drawpile's mainline code. You can find it [on GitHub](https://github.com/drawpile/Drawpile/releases/tag/continuous){:target="_blank"}.

Like the name implies, it's continually updated, so it usually changes multiple times a week. As such, it always has the latest features and fixes on it, but is also thoroughly untested, so there may be occasional breakage that didn't get caught in preliminary testing.

If you want to help Drawpile's development, feel free to use these releases and report any issues you find!

The version number of continuous releases that you find under Help → About Drawpile always has an gibberish-looking identifier at the end that can be used to find out what state of the code it was built from. For example, `2.2.2-beta.1-517-gb6892d69a`.
