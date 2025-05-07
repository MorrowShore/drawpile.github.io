---
layout: drawpile_post
title: "Dev Update: Week 10 and 11 of 2025"
date: 2025-03-16 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks have mostly been spent fixing some bugs and last-step issues before the release of version 2.2.2. There's also been significant work on enabling faster saving and loading of canvases, autosaving. timelapses and more through a new file format.

## Preferred Sockets

Drawpile now supports a new `w` query parameter in invite and session links. This will make it connect via WebSocket instead, without having to manually type out a different link. So, for example, entering `pub.drawpile.net?w` into the join dialog will make Drawpile connect to `wss://pub.drawpile.net/drawpile-web/ws`.

Server owners can enable a new "prefer WebSockets" option. This will make supporting clients automatically append the query parameter to invite links.

The reason you'd do this is that WebSockets run over regular HTTP(S) connections, which is overall more compatible with proxy stuff like Cloudflare. Some internet service providers or weird networks also like to block connections they don't know, but WebSockets will work on them. There isn't any downsides to using WebSockets I think, it's also just a message-based protocol over TCP like Drawpile's own protocol is, so it probably makes sense to start moving things over.

## Browser Language

The web browser version of Drawpile no longer pretends that you can change the language in the preferences. It had the drop-down selection there, but it didn't actually do anything. This was reported by Albano Battistella [on GitHub](https://github.com/drawpile/Drawpile/issues/1457){:target="_blank"}.

You *can* change the language by adding a `locale` parameter to the URL now though. For example, <https://web.drawpile.net/?locale=de_DE> gives you German. It would probably make sense to check whether the user's locale is set to something non-English and then offer them a language selection before starting Drawpile.

The minor problem here is that several languages don't work properly in the browser because of font or input issues, so not too sure which should be made available this way.

## Project File Format

Drawpile has been using Open Raster Archive (ORA) files to save canvases for a long time. The format is not specific to Drawpile, it was originally created by Krita developers to make a format for archiving and interoperation. And it's really good at that, it's really just a ZIP file with PNG images inside and an XML file describing how to arrange them in layers. It's also pretty small compared to other formats, an equivalent PSD or KRA or XCF file tends to be two to three times as large.

However, there's also some things it's not good at. Since ZIP files store a table of contents at the end of the file, you can't easily append stuff to them. For Drawpile, that means we can't use it for session recordings or automatic recovery in case the program terminates without saving. It's also somewhat slow save and open, which is mostly down to encoding and decoding the PNG files. Well, okay, Drawpile is still pretty fast in this regard compared to other programs, especially if your machine has many CPU cores, but it can still take kind of an annoyingly long time to save stuff sometimes. And lastly, in the browser and on Android, having to deal with external files at all isn't a great experience, since those platforms expect data to be stored inside the system instead.

To solve this, I've been thinking about and working on a new file format for Drawpile to address these issues. It's not going to replace ORA, since that's still a great format for archiving and interoperability, it'll just be another format for stuff that ORA doesn't cover.

Right now, saving and loading a canvas is already implemented. Results are pretty good, from my testing, opening and saving the format is over 3000% (not a typo, three thousand percent) faster than ORA. It's even slightly faster than exporting a JPEG. The sizes end up pretty much in the middle between equivalent ORA and KRA files, so still pretty compact compared to pretty much every other file format.

### Format Details

The file format uses SQLite as a container, like pretty much every new file format should. SQLite files are databases that allow querying, adding and removing data on the fly, so it can be used both for session recordings and for storing a single canvas state. The format is much more resilient to data loss and has better recovery options than ZIP files do. Pixel and recording data is compressed using zstd because it's very fast.

The database contains a table of sessions. One gets opened when you start working on the file and then closed when you stop. The purpose of this is mostly for the sake of auto-saving: if there's an open session, something probably crashed and there's data to recover (except if another process has the file open already, but SQLite's got that covered.)

Then there's a table of messages. This is just for session recordings: a big list of drawing commands. These are used for auto-saving too, since you can just play back the commands to restore a canvas. That means the format doesn't need to save periodically, it can just keep appending stuff to the table as it happens. If your computer shuts off or Drawpile terminates unexpectedly, you should be able to roll back to the instant that it happened and not lose any work. More than that, you can even travel back in time and get stuff back from the past, since the entire history is saved. In theory, you could even rewrite the history and, for example, apply brush strokes to a different layer if you accidentally lined on your sketch layer or something. The list of messages can also be used to create a timelapse and easily tell you how long it took you to draw a picture.

And third, there's a few snapshot tables. A snapshot represents a single canvas state, basically what you'd save in an ORA file. It's got a bunch of other tables associated with it to store the layer tree, timeline, annotations and such. Pixel data is stored in the form of 64x64 pixel tiles with 8 bits per channel, since that's very close to how it's represented in Drawpile internally and is suited really well to be spread across multiple CPU cores and make it go fast. I also tried saving it in the actual internal 15 bits per channel format to skip the conversion, but that just ended up being slower. Which I guess makes sense, having twice as much data to (de)compress and read/write is more work than the small bit of optimized math.

There's two variants of the format. One of them is a full "project" file with sessions and recordings and the whole shebang in it, the extension being `dppr`. The other is just a single canvas snapshot with the extension `dpcs`, for when you don't care to have an entire history. The only difference in their structure is that they use different numbers in their SQLite header. The database schema is identical, the snapshot one just leaves the session and message tables empty.

### Integration

How this stuff is actually put into Drawpile still needs some thinking. The snapshot file format the easiest and already implemented, it's just another format option in the save dialog. This on its own would already make a useful feature, since it saves and loads so much faster than ORA.

The way full project files will probably work is that opening a file will create a new, empty project in your application data directory (usually `.local/share`, `%APPDATA%` on Windows, "internally" on Android and the browser) where it will record to. Those files will be locked in exclusive mode, which means no other process can write to it simultaneously. Once you're done, they'll be deleted. When another instance of Drawpile starts, it can enumerate the project files and try to open each one. If a file is locked, then another instance of Drawpile is still alive and working on it. If not, there's something to recover.

When you save to an existing project file, the messages from the project in the application data directory get appended to it and a new snapshot added. SQLite allows attaching another database file, so it's got the facilities to grab data from one project database and sticking it into another. On Android and the browser, you would instead save to the internal storage directly, with some kind of in-application view where you can manage your projects. Saving an external file would involve exporting or downloading it.

There'd be some options under File where you can fiddle with your project. For example, File → Project and then sub-options to delete the history to shrink the size, create a timelapse, get statistics and whatever else.

If you have other ideas about this, [voice them somewhere](https://drawpile.net/help/){:target="_blank"}!

## Minor Additions and Bugfixes

The drawpile-cmd command-line tool now supports reading from an image rather than from a recording using the `-i`/`--image` parameter. It also supports PSD and WEBP now.

Hiding and showing docks (e.g. by hitting Tab) no longer messes up the reference dock's scroll position. This was reported by izzy [on Discord](htps://drawpile.net/discord/){:target="_blank"}.

Renaming a track now properly shows and pre-selects the previous name. This was reported by Greendyno [on Discord](htps://drawpile.net/discord/){:target="_blank"}.

On Android, the login dialog now resizes itself to a more sensible size to show you the session listings, rather than being so narrow that the titles get squashed into oblivion.

Kinetic scrolling via touch no longer makes the view jerk around when you move your finger over the scroll bar.
