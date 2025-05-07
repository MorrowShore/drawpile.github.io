---
layout: drawpile_help
title:  "Hosting Sessions"
description: "How to host drawing sessions in Drawpile."
date: 2023-09-29 00:00:00 +0200
category: "help"
tag: help common
---

If you want to start a new online drawing session, you can do so via the Host Session page in the start dialog. This article walks you through the process.

If you have trouble with this, you can ask for help on Discord or the web chat! You can find links to those [on the main help page](https://drawpile.net/help/).

* Table of contents
{:toc}

## Quick start

After you run, click on the Host Session tab. If your session allows adult content, check the **not suitable for minors (NSFM)** option.

You don't need to touch any other settings, just click the **Host** button at the bottom.

You'll be prompted to accept [public server rules](https://drawpile.net/communities/drawpile.net/ccg/) and to either log in or continue without an account.

Afterwards, your session will be up with a blank canvas and you'll be prompted to invite people to it.

![Host page]({{ "/assets/img/help/hostdialog.webp" | relative_url }})

## Hosting a canvas

If you don't want to host an empty canvas, you can open a file first or fill the canvas with whatever you want to start with. Then select Session → Host from the top menu and continue as above.

![Host menu]({{ "/assets/img/help/sessionhost.webp" | relative_url }})

## Hosting a public session

By default, sessions are password-protected, which means only people that you give an invite link to can join. You can remove the password by changing the setting from Personal to Public when hosting, or by removing the password from an existing session in the session settings.

Public sessions must be moderated properly. Take a look at [the help page on running public sessions](/help/server/sessionoperation) to learn about the tools

Failing to properly handle public sessions will likely get it reported and lead to an administrator step in.

## Ending a session

Sessions will end on their own after everyone leaves. They may linger for a bit to give the last person to leave a chance to reconnect.

If you want to make sure no one reconnects for some reason, you can go into Session → Settings and change the session password. This will render any invite links invalid.

## Community servers

There's some [official community servers](https://drawpile.net/communities/) that also let you host sessions on them. They have different themes, features and restrictions.

When you host on these servers, you have to adhere to the rules they present you with and to [the common community guidelines](https://drawpile.net/communities/drawpile.net/ccg/)!

## Other servers

Drawpile is public software, so anyone can set up a server for it. If you want to do it yourself, take a look at [the help pages on server hosting](/help/server/).

These servers aren't related to drawpile.net and they make their own rules.

## Builtin server

<div class="notification" markdown="1">
Hosting with the builtin server is **not recommended**. It comes with numerous downsides over hosting on a proper server.

Setting up your home network to allow connections is difficult, on some connections even impossible. Even if you set it up correctly, many internet providers don't allow connection to residential networks. Web browsers won't be able to connect to your session either, other people have to use the desktop or mobile application. If your computer turns off or the application crashes, the session will go down. There's also security and privacy implications of making your computer visible to the internet and giving other people your home IP address.

You should host on [the public Drawpile server](#quick-start), on [a community server](#community-servers) or on [some other dedicated server](#other-servers) instead.
</div>

When hosting on your own computer, you need to configure your computer to be visible to the internet.

If you already know what you're doing: **forward TCP port 27750**.

Otherwise, here's the long explanation. This gets a little technical.

Note that if you are in a dormitory, hotel or some other kind of internet that you don't own, you probably don't have the necessary access to set things up. Also, some internet connections don't allow you to host servers altogether.

Your computer is not connected directly to the internet, instead it's connected to a box called a router, along with any other device in your home. This way, you only need one internet connection for all your devices.

However, that means when someone else tries to connect to your computer from the internet, they will instead reach your router. This router will reject the connection, because it doesn't know which device in your home it's supposed to go to.

So you have to tell your router to send connections that are trying to reach your Drawpile session to your computer. This is called "port forwarding", because your router takes the connection and forwards it to your computer.

![Diagram of how your computer is connected to the internet]({{ "/assets/img/help/router.webp" | relative_url }})

Unfortunately, every router is different, so there's no single set of instructions we can give here. You have to forward TCP port 27750 to your computer. You can normally do this on your router's configuration page, which is usually at <http://192.168.1.1/>{:target="_blank"} or <http://192.168.178.1/>{:target="_blank"} or <http://10.0.0.1/>{:target="_blank"}.

If you can't get this working, you can ask for help on Discord or the web chat. When someone has time they can walk you through it. Take a look at [the help page](https://drawpile.net/help/) for links to those.

If you use some service to check if your port forward worked, **make sure you're actually hosting a session when you do it**. If you're not running anything that could be reached, it will always seem like your computer isn't reachable!
