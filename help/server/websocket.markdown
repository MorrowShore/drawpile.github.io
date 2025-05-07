---
layout: drawpile_help
title: "WebSocket Server Setup"
description: "How to set up drawpile-srv so that users can connect to it from the Drawpile web browser client."
date: 2024-02-20 00:00:00 +0200
category: "help"
tag: help server
---

This document describes how to configure a [dedicated Drawpile server](dedicatedserver) to be accessible from the web browser version of Drawpile.

If you just want to host a single drawing session that allows others to join via the browser, you can do so [on the community servers](https://drawpile.net/communities/) that support it.

Check out [the main help page](/help/) on how to get in contact if you have trouble setting this stuff up, have any questions about it or want to provide improvements to this documentation!

* Table of contents
{:toc}

### Requirements

To let people join sessions on your Drawpile server via the web browser client, you need the following:

* A Drawpile server, drawpile-srv version 2.2.1 or newer.
* TLS certificates. Ideally you also have a domain name for these, but self-signed ones will also work if your users go through the detour of adding an exception for them.
* Some web server that lets you reverse proxy WebSocket connections, such as nginx.

How to set these up will be described in the following sections.

You don't need to host the web browser client yourself, the one provided through web.drawpile.net works for any server. You only need to provide the WebSocket interface.

### Drawpile Server

Make sure your drawpile-srv version is at 2.2.1 or newer. If you compile it yourself, make sure you have QtWebSockets installed when you do. The configure step will tell you if WebSocket support has been enabled or not.

Add [the --websocket-port option](serverconfig#websocket-listen-port), along with a port of your choice. The documentation uses 27751, so if that's available, you can use it.

If you're using systemd sockets, pass the WebSocket port as the third socket (the first one is for TCP, the second one is for the HTTP web admin API.)

If you want to run your reverse proxy on the same machine as your drawpile-srv and don't have a firewall, you can also pass `--websocket-listen 127.0.0.1` to [restrict connections to ones coming from localhost](serverconfig#websocket-listen-address). Note that if you're using Docker, your reverse proxy is probably *not* inside the container, so don't add this option there!

Restart your drawpile-srv afterwards.

### TLS Certificates

If you have a domain name, you can use [certbot](https://certbot.eff.org/){:target="_blank"} to get Let's Encrypt certificates for it. If not, you can generate self-signed certificates yourself.

TLS certificates are *required*. The web browser client can only run on HTTPS websites, which can only connect to TLS-secured WebSockets. The reason that it can only run on HTTPS websites is because browsers mandate [secure contexts](https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts) for multithreaded applications, which Drawpile is.

### Reverse Proxy

A reverse proxy will take a WebSocket connection to your server and forward it to drawpile-srv. It is used to apply the TLS certificates and is generally a good idea, since having a web server gives better security and doesn't restrict you to only running Drawpile on that machine.

The reverse proxy is *required* because drawpile-srv does not currently support TLS for its WebSockets directly (but someone could [contribute it](https://docs.drawpile.net/help/development/contributing)), and TLS is required for the reasons given above.

If you're using nginx, you can use the following configuration. Put it into a server block that's listening to port 443 and has TLS certificates set up in it.

*Note*: the configuration below has been updated on February 15, 2025. If you're getting 403 errors trying to connect from the Drawpile application, you may need to change the Origin check.

```nginx
# Don't change the location, invite links only work with this one.
location /drawpile-web/ws {
    # This if block is optional, but recommended. WebSockets don't have a
    # same-origin policy, so without checking the origin, any website can
    # make a connection. Also, DO NOT change this to your own hostname, it
    # will make the client on web.drawpile.net and the Chinese mirror on
    # web.foxdice.cn stop working. If you want to allow other origins, you
    # can add them here of course. This configuration also allows connections
    # from the Drawpile application using an empty origin, whereas browsers are
    # guaranteed to always set a non-empty origin.
    if ($http_origin !~ "^(https://web\.(drawpile\.net|foxdice\.cn))?$") {
        return 403;
    }
    # Same port as you passed to drawpile-srv via --websocket-port.
    proxy_pass http://localhost:27751;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header X-Real-IP $remote_addr;
}
```

Note that if you have any kind of `proxy_pass_*` directives in the root of the `server` block, you probably have to revert them inside of the above `location` block. For example if there's a `proxy_pass_request_headers off;` at the root, you need to add `proxy_pass_request_headers on;` to Drawpile's `location` block.

Other web servers should work similarly, refer to their documentation on how to set up a WebSocket reverse proxy for them.

### Trying It Out

Use the Drawpile 2.2.1 desktop client to host a session on your server and copy the invite link. When you follow it, it should give you a button on the page that gives you the option to join via the browser.

If that's not working and you're trying to diagnose what's wrong, try using Firefox. It has better error messages for this stuff than Chromium-based browsers.

And you can of course get in touch if you need help, refer to [the main help page](/help/) on how to get in contact.

### Hosting the Web Browser Client (Not Required and Discouraged)

Hosting your own version of the web browser client is possible, but **strongly discouraged**.

Drawpile already provides a web browser client at <https://web.drawpile.net>. It works the same as the desktop version of Drawpile and is able to connect to any properly-configured server. It's just a bunch of static files, there's no server-side logic to it. Connections you make to other servers are direct WebSocket connections, drawpile.net isn't involved.

That means hosting your own version just gives you a copy of the same client, basically like hosting your own downloads of the desktop version of Drawpile, which is pretty pointless and causes us headaches if you don't keep it up to date. It also is torturous to users because all of their settings are per-domain, so they have to configure everything multiple times.

However, you may want to host your own client as a backup in case web.drawpile.net goes down, because you are in a region or organization where that site is not accessible or because you want to make customizations to it that aren't accepted into the main application. In that case, hosting your own client makes sense, but please make sure we're not left holding the pieces. Also, if you make customizations, you must also release the source code to those changes in accordance with the GPL!

To build the client, look [at the Building from Source page](/help/development/buildingfromsource#webassembly). That page assumes you're building for development purposes, so replace any instances of `debug` with `release`.

To host the client using nginx, you need to set up the wasm type correctly. In nginx.conf in the http section, add:

```nginx
types {
    application/wasm wasm;
}
```

To set up the nginx site, use a configuration like the following (replace drawpile.example.com):

```nginx
map $sent_http_content_type $webexpires {
    default max;
    text/html off;
}

map $sent_http_content_type $webcachecontrol {
    text/html 'no-store';
}

server {
    server_name drawpile.example.com;
    server_tokens off;

    root /path/to/client/files;

    location / {
        add_header "Cross-Origin-Resource-Policy" "same-site";
        add_header "Cross-Origin-Opener-Policy" "same-origin";
        add_header "Cross-Origin-Embedder-Policy" "require-corp";
        add_header "Cache-Control" $webcachecontrol;
        expires $webexpires;
        limit_except GET {
            deny all;
        }
    }

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/drawpile.example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/drawpile.example.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = drawpile.example.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    server_name drawpile.example.com;
    server_tokens off;
    listen 80;
    return 404; # managed by Certbot
}
```

The caching stuff is necessary to avoid clients redownloading the large asset and wasm stuff every time, but always reload the index page in case of updates.

The headers are mandatory to get a secure context on the page.
