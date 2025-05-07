---
layout: drawpile_help
title: "Installation Issues"
description: "How to to troubleshoot and solve installation issues on various platforms"
date: 2024-10-26 00:00:00 +0200
category: "help"
tag: help tech
---

This page tries to provide troubleshooting instructions for installing Drawpile on different operating systems.

* Table of contents
{:toc}

## macOS

Installing Drawpile on macOS will usually give you a message like "application cannot be opened because it is from an unidentified developer" or even the scare-mongering message "Apple could not verify 'Drawpile' is free of malware that may harm your Mac or compromise your privacy."

This isn't because Drawpile is insecure, it's just because we don't pay Apple protection money every year for an official developer account. We may this extortion fee in the future, but for now you have to trust Drawpile manually.

What you need to do for this depends on which version of macOS you're running. To find out, click the Apple logo on the top-left and click on "About this Mac". It will tell you the version here.

### On macOS 14 Sonoma and earlier

Command-click or right-click on Drawpile and select **Open**. You will see the same message again, but now there will be a new option to open the application anyway.

### On macOS 15 Sequoia and later

Unfortunately, Apple has made the process much more roundabout on later versions of macOS.

1. Attempt to open Drawpile and have it fail. It will tell you that it could not be opened. *Do not move it to trash*, just click **Done**.

![Dialog saying Drawpile could not be opened]({{ "/assets/img/help/sequoia1.webp" | relative_url }})

{:start="2"}
2. Open the System Settings and naviate to "Privacy & Security" (you can search for or scroll to it.)

![Privacy & Security in System Settings]({{ "/assets/img/help/sequoia2.webp" | relative_url }})

{:start="3"}
3. In the Privacy & Security panel, scroll to the warning that says "'Drawpile' was blocked to protect your Mac." and click on **Open Anyway**.

![Privacy & Security in System Settings]({{ "/assets/img/help/sequoia3.webp" | relative_url }})

This section and screenshots were contributed by [Axocrat](https://twitter.com/axocract){:target="_blank"}.
