---
layout: drawpile_help
title: "Architecture"
description: "An overview of the different pieces of Drawpile and where to find what code."
date: 2024-03-30 00:00:00 +0200
category: "help"
tag: help development
---

This page explains how the different pieces of Drawpile and its ancillary software fit together and where you can find what code.

* Table of contents
{:toc}

## Overview

All of the different code repositories can be found under [the Drawpile organization on GitHub](https://github.com/drawpile){:target="_blank"}. The following are the relevant ones, click on any of the links to be taken to the relevant section below:

* [The Drawpile repository](#drawpile) contains the client, the dedicated server and the command-line.
* [The listserver repository](#list-server) contains the listing server, which is what makes session browsing work.
* [The dpwebadmin and drawpile-admin-webui repositories](#web-admin-uis) contain the web browser interfaces for administrating drawpile-srv and the listing server.
* [The abusereport repository](#abuse-report-relay) is a tiny service that takes reports from drawpile-srv and relays them to some notification service.
* [The dpserver repository](#all-in-one-dedicated-server-docker-package) contains the all-in-one Docker page for setting up a Drawpile server.
* [The drawpile.github.io repository](#documentation) contains the documentation you're reading right now.

## Drawpile

Repository: <https://github.com/drawpile/Drawpile>

This is where the client (hereto referred to as "drawpile", with a lowercase "d"), dedicated server drawpile-srv and the command-line tools dprectool, drawpile-cmd and drawpile-timelapse live.

Most of the code is in subdirectories of the `src` directory. The picture below shows how they're related:

![Drawpile architecture diagram]({{ "/assets/img/help/architecture-drawpile.svg" | relative_url }})

{% comment %}
@startuml
package Drawpile {
    [drawdance]
    [tools]
    [libshared]
    [libclient]
    [libserver]
    [thinsrv]
    [desktop]
    tools --> drawdance
    libclient --> libshared
    libclient --> drawdance
    libserver --> libshared
    libserver --> drawdance
    desktop --> libclient
    desktop ..> libserver
    thinsrv --> libserver
    note right of desktop : drawpile
    note left of thinsrv : drawpile-srv
    note bottom of tools : dprectool\ndrawpile-cmd\ndrawpile-timelapse
}
@enduml
{% endcomment %}

### Paint Engine

At the bottom, you have **drawdance**. This is Drawpile's core paint engine as of version 2.2.0. It is written mostly in C, C++ is only used to interoperate with external libraries. It used to be a separate project outside of Drawpile, so it's more decoupled from it that other pieces. It is in turn split into further pieces:

* `libcommon` contains base library stuff like logging, threading, atomics etc. It's stuff you'd also find in Qt for example, but Drawdance has its own stuff to be usable without such a heavy dependency and for performance reasons.
* `libmsg` contains the protocol messages, along with bits to read and write them to files.
* `libengine` contains the actual paint engine, which has the bulk of logic for the canvas state and history, drawing operations, image file I/O etc.
* `rust` contains Rust bindings, used by the command-line tools.
* `generators` contains some code generation tools. Most interestingly is `protogen`, which generates the code for the protocol messages, and `bindgen.bash`, which generates the Rust bindings.

Particularly interesting bits are:

* The canvas state structures, which represent the canvas with all of its layers, annotations, timeline frames etc. They are [persistent data structures](https://en.wikipedia.org/wiki/Persistent_data_structure){:target="_blank"}, specifically inspired by [Clojure's transient data structures](https://clojure.org/reference/transients){:target="_blank"}. They can be found in `canvas_state.{h,cpp}`, `annotation.{h,cpp}`, `annotation_list.{h,cpp}`, `document_metadata.{h,cpp}`, `frame.{h,cpp}`, `key_frame.{h,cpp}`, `layer_content.{h,cpp}`, `layer_group.{h,cpp}`, `layer_list.{h,cpp}`, `layer_props_list.{h,cpp}`, `tile.{h,cpp}`, `timeline.{h,cpp}` and `track.{h,cpp}`.
* Figuring out what changed between two canvas states is done in `canvas_diff.{h,cpp}`. This is done using dirty checking, which is a fast operation for the above data structures.
* The rollback netcode is found in `canvas_history.{h,cpp}`. It deals with consolidating the local and server states. It's also where undo and redo are handled.
* Handling of drawing commands is in `ops.{h,cpp}`. There's usually one operation per drawing command.
* The connection between the frontend and the canvas history is found in `paint_engine.{h,cpp}`. It handles updating the view of the canvas at every tick and communicates back and forth.
* Rendering the canvas state into viewable pixels is done in `renderer.{h,cpp}`.
* Translating inputs to brush strokes is done in `brush_engine.{h,cpp}`.

### C++ Libraries

On top of the paint engine there's three libraries written in C++ and using Qt.

* `libshared` contains stuff that's used in both the client and the server, such as encryption stuff for the external authentication or C++ wrappers around the Drawdance message structures.
* `libclient` is the base library for the client, but does not (or at least isn't supposed to) contain any user interface stuff. The (wishful) intent is to allow for different interfaces other than the QtWidgets-based drawpile to be stacked on top of it, such as something QtQuick-based or a headless client.
* `libserver` is the base library for the dedicated server. It's also linked into the client to provide the builtin server.

### Client

The main client is found in the `desktop` directory, written in C++ and using Qt. The name is historical, the same "desktop" client is also used on mobile and in the browser.

There's quite a few subdirectories in here, so here's an abridged map for guidance:

* `main.{h,cpp}` contain the QApplication and main entry point.
* `mainwindow.{h,cpp}` contains the main application window. This class is unfortunately ginormous, but so far has resisted being split up.
* `settings_table.h` contains a big table of persistent settings. There's also another, smaller one in libclient.
* `filewrangler.{h,cpp}` are for handling files in the face of different platforms. On Android and in the browser, a bunch of special handling is required.
* `assets` contains the initial brush presets database, themes, palettes, sounds and icons. Somewhat confusingly, the icons are inside the `theme` directory (because there's a dark and light theme for them) and the themes are in the `*.color` files.
* `icons` also contains icons, but it's stuff like the program icon or the favicon in the browser.
* `dialogs`, `docks` and `widgets` contain the kinds of UI elements they say on the tin. The settings and start dialogs get their own subdirectories.
* `toolwidgets` contains the different pages of the "Tool" dock.
* `view` contains the "new" OpenGL canvas view and related classes.
* `scene` contains the "old" QGraphicsView-based canvas view (to be removed at some point in the future), as well as elements that get superimposed onto the canvas like annotations, selections, user markers etc.
* `ui` contains QtDesigner user interface files. The use of these is mildly discouraged, most new stuff just sets up its UI in code instead, since that requires less flipping back and forth between UI definition and code, doesn't cause as tricky merge conflicts, allows for dynamic UI (e.g. in the brush editor) and doesn't involve fighting against QtDesigner's many annoyances.
* `wasm` contains some HTML, CSS and JavaScript bits to put the application into the web browser.

### Dedicated Server

The server is found in `thinsrv`, also in C++ and Qt. As the name implies, it's a "thin" or "dumb" server, it does no processing of drawing commands.

This is an important architectural detail: **the server does not deal with the drawing protocol**. Any protocol messages with id >= 64 is off limits. This allows the drawing protocol to change without breaking compatibility between client and server. It also keeps resource usage low.

Features that touch anything related to drawing operations do not belong in the server. They must either be implemented client-side or offloaded into some form of optional, versionable sidecar application.

### Command-Line Tools

The tools `dprectool`, `drawpile-cmd` and `drawpile-timelapse' are found in the `tools` directory. They're written in Rust as static libraries, with a tiny part in C or C++ that just calls into this library.

## List Server

Repository: <https://github.com/drawpile/listserver>

The list server is responsible for providing the session listings in Drawpile's Browse tab and on websites, such as on drawpile.net's communities pages. It's written in Go and uses SQLite for persistence.

It can include sessions from one or more dedicated Drawpile servers as well as let people announce sessions on other servers. Its internal architecture isn't too exciting, it's a pretty small service.

## Web Admin UIs

Repositories: <https://github.com/drawpile/dpwebadmin> (old UI) and <https://github.com/drawpile/drawpile-admin-webui> (new UI)

There's two web admin UIs: an old one (dpwebadmin) and a new one (drawpile-admin-webui).

The old one is written in JavaScript Create React App (CRA) and provides a frontend for the dedicated Drawpile server. Maintaining a React application is awful though, it has way too many dependencies and changes constantly. At the time it was created, CRA was the recommended way to bootstrap a React application, today it's already been discontinued. So we don't particularly want to develop it further.

The new one is written in TypeScript. It uses Lit, a Web Components library. For CSS, it uses pico.css and Sass. Compilation and bundling is done with esbuild. At the time of writing, it only provides an admin frontend for the list server, but support for the drawpile-srv is intended to go into it as well.

## Abuse Report Relay

Repository: <https://github.com/drawpile/abusereport/>

As it says on the tin, this takes abuse reports from drawpile-srv and relays them to another service. At the time of writing, it supports only Discord. It's written in Python using aiohttp.

## All-In-One Dedicated Server Docker Package

Repository: <https://github.com/drawpile/dpserver>

Bundles together the Drawpile server, list server, abuse report relay, old web admin UI, an nginx and a static session listing site into an all-in-one setup via Docker. This mostly consists of shell scripts.

## Documentation

Repository: <https://github.com/drawpile/drawpile.github.io>

This is the website you're reading right now. It uses Jekyll and is hosted on GitHub Pages.
