---
layout: drawpile_post
title: "Dev Update: Week 34 and 35 of 2024"
date: 2024-08-31 00:00:00 +0200
category: "devblog"
tag: "@askmeaboutloom"
---

The last two weeks have mostly been spent on implementing streamed autoresets. This feature should get rid of those disruptive session resets by instead performing them in the background.

## Streamed Autoresets

Session autoresets is something that most people using Drawpile have seen: everything stops, a message in the chat appears that says "preparing for session autoreset" or similar. Then there's a pause where you can't draw, a bar fills up and, after a bit, session looks the same as it was. Usually.

This happens for technical reasons. There's already [an article on that](/devblog/2024/03/03/resets.html) explaining these resets in more detail. But in short: the way this works is annoying and inefficient, since it's a whole lot of work just to end up with the canvas looking like it did before.

Streamed autoresets fix this. Instead of pausing the session, they just happen on the fly. Only the client doing the autoreset actually sees that it's happening because they'll get a message in the bottom-right corner telling them that they're compressing the canvas and then uploading it. Everyone else just sees that the session size in the bottom-left corner goes down at some point, but there's no interruption and everyone can just keep drawing during it.

<video controls>
  <source src="{{ "/assets/vid/2024-08-31_streamreset.mp4" | relative_url }}" type="video/mp4"/>
</video>

The upside for clients is obvious: no annoying interruptions. But it's also better on the server-side, resets are a pretty expensive operation that use a lot of memory and require sending the entire session state to every connected client. Streamed autoresets can go straight into a file instead and don't require clients to be re-sent the state they already got.

Downside is that is uses slightly more disk space on the server, which is fine. And of course there's a potential for desync to go unnoticed if there's a bug somewhere… but that's probably preferable to session resets immediately chewing up the session and requiring that annoying banner at the top asking you if you want to save the previous state.

Note that this only refers to *auto*-resets. Manual resets via Session → Reset still work like they did before, since their whole point is to throw away whatever the server's state is and replace it with something you give to it.

### Compatibility

Streamed autoresets need the server to be updated to support it. At the time of writing, none of the official community servers have been updated to it, since the feature is still a bit too new for that and needs some smaller-scale testing first.

On the client-side, it's fully compatible. If there's an operator in the session that's on a client supporting streamed autoresets, they will happen. If no operator has a client that supports it, you'll get the old behavior.

### Explanation

This is a simplified explanation to show how streamed session resets work. It begins with the history, which contains a list of drawing commands, illustrated here as rectangles going from left to right. When somebody joins, they receive all these commands and once they caught up they see the canvas as it should be.

![Diagram of the canvas history with drawing commands]({{ "/assets/img/2024-08-31_stream1.webp" | relative_url }})

Now we start a streamed autoreset. This marks the point in the history where the stream begins and a new list of drawing commands is started. This list will contain a compressed set of commands that contain the current canvas as an image instead of a bunch of commands.

![Diagram of the canvas history with drawing commands]({{ "/assets/img/2024-08-31_stream2.webp" | relative_url }})

This stream list gets built up in the background, but people can keep drawing and those drawing commands are added to the history, just like they would be without the stream.

![Diagram of the canvas history with drawing commands]({{ "/assets/img/2024-08-31_stream3.webp" | relative_url }})

Once the stream is done, the commands that happened since it started are added to the end…

![Diagram of the canvas history with drawing commands]({{ "/assets/img/2024-08-31_stream4.webp" | relative_url }})

…and then the stream becomes the new history, with the previous history being discarded. When someone new joins, they will catch up to this new, shorter history instead. Since the starting point was synced up with the old history, they should get the same canvas as everyone that's already in the session.

![Diagram of the canvas history with drawing commands]({{ "/assets/img/2024-08-31_stream5.webp" | relative_url }})

### More Techy Explanation

This explanation goes in detail of how streamed reset process works. You can skip this part if you don't want all those nits and grits on you.

If there's an error anywhere in this process, the stream is discarded if it was open and the autoreset is retried. Errors include stuff like getting no response to an autoreset query, the session running out of space, a write error occurring, the resetting client leaving or them losing their operator status.

If a regular reset comes in during the stream, it is also discarded and the regular reset is processed instead.

#### 1. Autoreset Query

When the autoreset threshold on the session history is reached, the server sends out an autoreset query to all operators in the session. The query contains a unique correlator for this autoreset request. It then sets a 3 minute timeout and waits for responses.

Clients will respond to this query if they consider themselves caught up enough. Old clients will ignore the correlator and just send a blank ready response. New clients will send along an indicator of their capability to perform a streamed autoreset, their operating system (something like "windows" or "android", no versions or anything), their network quality (currently 0 if the client set their connection quality to "poor" in the network settings, 100 otherwise) and their last 64 latency measurements.

The server will collect this information into a list of candidates. Once the first response comes in, a 3 second timeout is started to give other clients the chance to also respond, but not delay the autoreset unnecessarily.

Once all responses have come in or any timeout is fired, the server moves to the next step.

#### 2. Autoreset Start

The server now picks the best candidate, which is determined by the following ranked sorting:

1. Clients that can do streamed autoresets are better than ones that don't.
2. Clients on desktop operating systems are better than Android or the browser, since the latter are stingy on memory and tend to fall asleep when the application or tab is put into the background. Unknown operating systems are in the middle.
3. Clients with higher network quality are better than ones with lower quality. No network quality is treated as zero.
4. Clients with a lower average ping are better than ones with higher average ping. If no average ping is provided, this comparison is skipped for those clients.
5. Clients that respond first are better than ones that respond later.

The sorting happens in this order, meaning that a user that supports streamed autoresets is preferred over any other user that doesn't, no matter their operating system, network quality or latency. If two users support streamed autoresets, the one with the better operating system wins, otherwise the one with the higher network quality and so on.

Once a candidate is determined, they are sent an autoreset request message. This message will contain the correlator again if it's a streamed reset, which is the case we're following here. Otherwise, it's an interrupting reset that behaves as it did before.

#### 3. Stream Start

The client receives the autoreset request and remembers the correlator in it. Then it sends a stream-reset-start message to the server.

The server will open a reset stream. For an in-memory history, they just go into a vector. For a file-backed history, a new recording file is opened and a new cache block is started at this point. A soft reset message along with a stream reset start response message are added to the history.

The client will receive these messages and send itself an internal stream reset start message, which will be processed by the paint engine right after the soft reset. The client will then start generating a reset image from the canvas state. This reset image consists of messages of the new type reset stream. These messages form a chunked gzip stream which decompresses into the actual messages making up the reset image.

#### 4. Stream Progress

Once the client has the reset image ready, they send the first message of it to the server. The server receives the message, decompresses it and adds the resulting messages to the stream. It then sends an acknowledgement to the client, which will prompt it to send the next message. This rigmarole with acknowledgements avoids the client saturating their upstream and being unable to draw properly.

If something goes wrong, like the server being unable to decompress or write a message, it sends a negative acknowledgement to make the client stop sending messages.

#### 5. Stream Preparation

Once the client is done sending all its messages, it sends a stream-reset-finish message to the server, at which point its duty is done.

The server finishes off any remaining decompression and puts the stream into a pending state. This state will usually resolve immediately, but if there's currently a client catching up and reading messages from the pre-stream area of the history, it will be delayed until that's completed.

#### 6. Stream Resolution

Once all clients are caught up sufficiently, the server appends the messages that have arrived to the history since the stream was started to the end of the stream. It then throws away the history and replaces it with the contents of the stream. For in-memory sessions, this just means swapping vectors. For file-backed sessions, it involves closing and deleting or archiving the previous recording, writing the new recording file to the journal and updating the cache blocks to the new recording.

The session size and autoreset thresholds are updated, a settings update is appended to the history and session size update is sent to all clients. Then the streamed reset is done.

## Color Dockage

There's been some changes to the color sliders, wheel and palette. All of them have a menu button at the top-left now.

The color sliders can now be configured to show all sliders at once instead of requiring you to toggle them. You can also toggle the presence of the hex input, in case that takes up too much room to fit on your layout. You can also change the color space here now, rather than having to go through the preferences for it.

<video controls>
  <source src="{{ "/assets/vid/2024-08-31_colorsliders.mp4" | relative_url }}" type="video/mp4"/>
</video>

For the color wheel, the settings that used to be in the tool preferences are also in the menu now. You can change the shape, rotation, direction and color space here now.

There's also now an option to change the alignment of the wheel, you can make it aligned to the top if that looks better in your layout. The default continues to be in the middle.

And the wheel will now preview your color while you're picking it. The top part of the color is what you're currently picking, the bottom part is the previous color for comparison, which is useful if you're trying to find the right values or contrasts when painting something. This preview can be turned off in the menu as well.

<video controls>
  <source src="{{ "/assets/vid/2024-08-31_colorwheel.mp4" | relative_url }}" type="video/mp4"/>
</video>

For the palette, the menu button is the only change. It got moved from being next to the palette drop-down to being at the top-left of the dock, to be consistent with all other color docks.

Various parts of this were suggested by MachKerman and MorrowShore [on Discord](https://drawpile.net/discord/){:target="_blank"}.

## Minor Additions and Bugfixes

Soft brushes are now centered on the cursor better, they got offset by an earlier fix. This was reported by Meiren [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Gridmap settings in MyPaint brushes no longer get messed up when first opening the brush settings editor. This was reported by Blozzom [on Discord](https://drawpile.net/discord/){:target="_blank"}.

Some default shortcuts were added. The numpad enter key now applies an action just like the regular enter key does, but you can still assign them separately (left-handed people tend to have a lot of shortcuts on the numpad.) Ctrl+Equals now zooms in just like Ctrl+Plus does, since either one makes sense depending on your keyboard layout. These were reported by MachKerman and Sinamer [on Discord](https://drawpile.net/discord/){:target="_blank"}.

The flood fill will no longer consider its own preview when using the merged image as the source. This was reported by Meru [on Discord](https://drawpile.net/discord/){:target="_blank"}.
