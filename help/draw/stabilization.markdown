---
layout: drawpile_help
title: "Stabilization"
description: "Stabilizer, smoothing and jag compensation in Drawpile."
date: 2024-02-28 00:00:00 +0100
category: "help"
tag: help draw
---

Drawpile has a few different ways of stabilizing your brush strokes. You can configure smoothing in the preferences and toggle jagged curve compensation so that you have a sensible baseline for your drawing tablet. On top of that, you can set the stabilizer or smoothing for individual brushes, so that for example your line brush is stabilized, but your paint brush is not.

* Table of contents
{:toc}

## Baseline Preferences

Different tablet devices have very different levels of precision. For example, Wacom devices report every tiny movement to Drawpile all the time, whereas Android devices are much coarser.

Under Edit → Preferences under the Tablet tab, you can adjust the following settings until they feel right for you:

**Global smoothing** will use multiple points reported by the tablet to make a single point of your stroke. The higher the value, the smoother your strokes are, but too high values may end up feeling slow.

**Compensate jagged curves** helps against fast brush strokes becoming jaggy. This is especially important for tablets with coarse input. On more precise tablets, this shouldn't make a difference either way.

Try to find settings that feel right for most brushes. That way, you don't have to mess with each individual brush's stabilization settings as much.

![Input preferences for smoothing and jagged curve compensation]({{ "/assets/img/help/stabilizerprefs.webp" | relative_url }})

## Brush Stabilization

Brushes can be stabilized either with a **Time-Based Stabilizer**, similar to Krita and Paint Tool SAI, or with **Average Smoothing**, which just averages multiple points from the tablet. The time-based stabilizer has better stabilization, but may feel comparatively slow. You can toggle between these two in the brush settings dock, see the screenshot below.

The higher you turn the **Stabilizer** or **Smoothing** slider, the stronger the stabilization will be. Very high values may end up feeling delayed.

The **Finish Strokes** setting will make the stabilizer draw to the end of your stroke once you lift your pen. If you don't want this behavior, you can toggle it off.

By default, stabilizer settings are saved *in your brush*, similar to how it works in MyPaint. If you don't want this, you can disable the **Synchronize With Brush** option. This will make the stabilizer independent from the brush, similar to how it works in Krita.

![Stabilizer brush settings]({{ "/assets/img/help/stabilizersettings.webp" | relative_url }})
