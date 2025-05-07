---
layout: drawpile_help
title: "Dedicated Server"
description: "How to use drawpile-srv, Drawpile's dedicated server."
date: 2024-01-13 00:00:00 +0200
category: "help"
tag: help server
---

Drawpile's dedicated server is called drawpile-srv. It lets you run your own server, just like [pub.drawpile.net and other community servers](https://drawpile.net/communities/). It can be run on pretty much any kind of Linux server, be it some cheap virtual server or even a Raspberry Pi. It also works on Windows and macOS.

If you just want to host a drawing session, refer to [the hosting help page](https://docs.drawpile.net/help/common/hosting) instead. You don't need to set up your own server for it.

* Table of contents
{:toc}

## Installing the Server

If you want to use Linux and set up a full Drawpile server from scratch, take a look at the [Server Setup with Docker page](docker). It will set up a Drawpile server, a listing server and web administration for it. If you just want a bare Drawpile server and nothing else, just running [the AppImage](https://drawpile.net/download/#Linux) with `--server` as the first argument may be enough for your purposes.

On Windows, the dedicated server is provided separately from [the Extras section on the download page](https://drawpile.net/download/#Archive).

On macOS, it's included in [the regular disk image on the download page](https://drawpile.net/download/#OSX).

## Compatibility

The 2.x series servers are compatible with all 2.x.x series clients, with the caveat that features introduced in newer servers may not be accessible from older clients.

Run `drawpile-srv --version` to see the protocol version number of the server. See the [compatibility chart](/help/development/versioncompatibility) for details.

## Starting the Server

To start the server, run `drawpile-srv` on the command line and provide the desired startup configuration via command-line parameters and environment variables. All the options are described [on the server configuration help page](serverconfig). You can also get a list of them by running `drawpile-srv --help`.

When the application is started with no command line options, or explicitly with the option `--gui`, it will run in graphical mode. The graphical interface is limited and does not allow you to configure all settings. Proper installations should use headless mode instead, with administration done [through a web interface](#web-admin-interface).

The GUI frontend can also act as a remote administration tool by starting it with command line arguments `drawpile-srv --gui --remote APIURL` or by right-clicking on the status tray icon.

## Configuration

There are two options for storing server configuration: a text-based [configuration file](#configuration-file) and an SQLite [database](#database).

When using the configuration file, the settings can only be changed by changing the file manually, the server will reload it when it detects a change. If you want to use the remote admin API, choose the configuration database.

The full list of configuration options can be found [on the server configuration help page](serverconfig).

### Configuration File

The configuration file uses a simple INI style format.

Example:

```
[config]
sessionSizeLimit = 15MB
sessionCountLimit = 25
idleTimeLimit = 3h
title = Welcome to my test server!
announceWhiteList = true

[announceWhitelist]
https://drawpile.net/api/sessions/

[ipbans]
192.168.1.1
10.0.0.1/16

[users]
moderator:plain;qwerty123:MOD,HOST
```

#### [config] section

This is where [the server configuration options](serverconfig) go. Each line is in a `key=value` format.

#### [announceWhiteList] section

In this section you can list the acceptable announcement server URLs. Remember to also add `announceWhitelist=true`
to the `[config]` section as well!

Tip: Leaving this section empty but setting `announceWhitelist` to `true` will disable session announcements entirely.

#### [ipbans] section

In this section you can list the IP addresses and subnets that are banned from the server.
Both IPv4 and IPv6 style addresses are supported.

Note: Write the IP in the form it appears in the server log.

#### [users] section

In this section you can list registered user accounts.
The syntax is `username:password:FLAGS`

Currently, the following password formats are supported:

* plaintext: `plain;PASSWORD`, where `PASSWORD` is the password in plain text.
* Salted SHA1: `s+sha1;SALT;HASH`, where `SALT` is the hex-encoded salt and `HASH` is the hex-encoded SHA1 hash of the salt concatenated with the password.
* PBKDF2: `pbkdf2;1;SALT;HASH`, where `SALT` is the base64-encoded salt and `HASH` is the base64-encoded PBKDF2 derivation as given by `QPasswordDigestor::deriveKeyPbkdf2`.
* Sodium password hash: `sodium;HASH`, where `HASH` is a base64-encoded value from libsodium's `crypto_pwhash_str`.

Prefixing the password field with `*` will mark the username as banned. E.g. `admin:*plain;abc123;MOD`

Supported user flags are:

* MOD - User is a moderator (can enter locked and password protected sessions and has permanent OP status.)
* HOST - May host sessions when `allowGuestHosts` is set to `false`.
* BANEXEMPT - Overrides server bans, useful if you had to apply a broad IP range ban that hit an innocent user that happens to fall into that range. This does *not* affect session bans.
* GHOST - Makes the user a ghost. They must also have the MOD flag for this to work. Ghosts can join sessions as "the server", without startling users because someone unknown joined their passworded session. Ghosts are not truly hidden, any user can see them joining or leaving in the session event log.
* WEB - May join via WebSockets when `allowGuestWeb` is `false`.
* WEBSESSION - May change sessions' WebSocket allowence when `allowGuestWebSession` is `false`.

See also [external authentication](#external-authentication) for an alternative way to have user accounts.

### Configuration Database

The configuration database is similar to the configuration file, but each section is a table
in an SQLite database. It can be edited manually with the `sqlite3` command, but its real
strength is that it can be easily modified by scripts and the server itself at runtime.

The database contains the following tables:

* settings (equivalent to the `[config]` section)
* listingservers
* ipbans
* users
* serverlog

When started in graphical mode, the server always uses a configuration database.
The location of the database depends on the operating system:

* Linux: `~/.local/share/drawpile/drawpile-srv/guiserver.db` (or `$XDG_DATA_HOME/drawpile/drawpile-srv/guiserver.db`)
* Windows: `C:\Users\%USERNAME%\AppData\Local\drawpile-srv\guiserver.db`
* macOS: `~/Library/Application Support/drawpile-srv/guiserver.db`

## TLS

The dedicated server supports TLS encrypted connections. You can use [Certbot](https://certbot.eff.org/){:target="_blank"} to generate Let's Encrypt certificates. You need a domain for this, an IP address is not enough.

Once you've done so, pass the following two parameters to drawpile-srv (replace `YOURDOMAIN` with your domain name):

* `--ssl-cert /etc/letsencrypt/live/YOURDOMAIN/fullchain.pem`
* `--ssl-key /etc/letsencrypt/live/YOURDOMAIN/privkey.pem`

Do *not* use `cert.pem` instead of `fullchain.pem`. Some systems will be able to cope with this and retrieve the certificate chain from elsewhere, but others won't and will get strange errors trying to connect.

## Using Docker

An easy way to run the server is to use Docker. Try it out:

```sh
$ docker run -it --rm -p 27750:27750 drawpile/drawpile-srv:2.2
```

This will download the server image from docker hub and run the server. The `-p 27750:27750` argument
publishes the default drawpile port from the container so clients can connect to it.

A more realistic example:

```sh
$ docker run -dt --name "drawpile-server" \
    -p 27750:27750 -p 127.0.0.1:27780:27780 \
    -v dpsessions:/home/drawpile \
    --restart always \
    drawpile/drawpile-srv:2.2 \
    --sessions /home/drawpile/sessions \
    --database /home/drawpile/config.db \
    --web-admin-port 27780 \
    --web-admin-access all
```

The above does the following things:

* Creates a container named `drawpile-server` from the `drawpile-srv:2.2` image pulled from Docker Hub and runs it in the background
* Publishes port 27750 on all network interfaces and 27780 (the admin API) on loopback only
* Mounts the named volume `dpsessions` at `/home/drawpile` inside the container. This allows hibernated sessions to live through container restarts
* Automatically restarts the container if if shuts down
* Enables file backed sessions, database configuration and web admin API
* The web admin API is limited to connections from localhost by publishing it only to 127.0.0.1. Inside the container, the `all` access mode must be used.

## Using systemd

To use systemd support in drawpile-srv, you must have `libsystemd-dev` or similar installed and build it with `-DINITSYS=systemd`. Doing so will install `drawpile-srv.service` and `drawpile-srv.socket`.

After installing it, you must set up an override file for it to configure it properly. To do so, run `sudo systemctl edit drawpile-srv.service` and set it up as follows. **Make sure you replace the following:**

* `***parameters-go-here***` with any command-line parameters you want to pass to drawpile-srv.
* `***linux-user***` with the Linux username under which the server should run. You should create a new user for this!
* `***linux-group***` with the Linux group, probably the same as the username.
* `***web-admin-username***` and `***web-admin-password***` with a username and password of your choosing to access the web admin API.

```systemd
[Service]
ExecStart=
ExecStart=/usr/local/bin/drawpile-srv ***parameters-go-here***
User=
User=***linux-user***
Group=
Group=***linux-group***
Environment=DRAWPILESRV_WEB_ADMIN_AUTH=***web-admin-username***:***web-admin-password***
```

The server can be started directly with `systemctl start drawpile-srv.service` or by socket activation using `systemctl start drawpile-srv.socket`. When socket activation is used, the server is started on-demand when the first client connects. Note that when using SA, the `--port` and `--listen` parameters are ignored. The listening address is configured in the `drawpile-srv.socket` unit file. The first socket provided is the TCP port, the second one is the web admin port, the third is the WebSocket port.

Use `systemctl enable drawpile-srv.service` to automatically start the server on boot. This will enable socket activation automatically. If you don't want that, disable it using `systemctl disable drawpile-srv.socket`. (This will probably change in the future, since it's annoying to have to enable both and then disable one.)

## Using the AppImage with systemd

The current server AppImage does not have systemd support compiled in, so it does not support socket activation, but you can run it via systemd anyway.

You can use is a sample unit file that works with it, place it in `/etc/systemd/system/drawpile-srv.service`. **Make sure you replace the following:**

* `***parameters-go-here***` with any command-line parameters you want to pass to drawpile-srv.
* `***linux-user***` with the Linux username under which the server should run. You should create a new user for this!
* `***linux-group***` with the Linux group, probably the same as the username.
* `***web-admin-username***` and `***web-admin-password***` with a username and password of your choosing to access the web admin API.

```systemd
[Unit]
Description=Drawpile dedicated server
After=network.target
Documentation=man:drawpile-srv

[Service]
ExecStart=/usr/local/bin/Drawpile.AppImage --server ***parameters-go-here***
Type=simple
Restart=always
User=***linux-user***
Group=***linux-group***
Environment=DRAWPILESRV_WEB_ADMIN_AUTH=***web-admin-username***:***web-admin-password***

[Install]
WantedBy=multi-user.target
```

Use `systemctl enable drawpile-srv` to automatically start the server on boot.

## Persistent Sessions

A session is normally deleted after the last user logs out.
When the `persistence` configuration setting is set to `true`, sessions are allowed
to continue even after the last user has left. Persistence must also be enabled by
the user hosting the session.

To prevent old sessions from piling up, it is a good idea to set an idle time limit.
Setting this option will cause sessions that have been idle for more than the allowed
time to automatically terminate. (Note that this applies to sessions with users still in them
as well, if no-one is drawing anything or even chatting.)

It is generally a good idea to use file backed sessions when persistence is enabled,
as it allows the sessions to survive a server restart and frees up memory when
no one is logged in.

## Session Recording

If a recording path is set, the server will make a recording of every session.
For example: `drawpile-srv --record ~/sessions/%a.dprec` will save each session
in the ´sessions` directory under the user's home directory.

The following placeholders can be used in the recording path:

 * `%d` - current date (YYYY-MM-DD)
 * `%h` - current time (HH.MM.SS)
 * `%i` - session ID
 * `%a` - session Alias (or ID if alias is not set)

If a file with the same name already exists, a number is added to the end of the name.
A new recording is started every time the session is reset.

When using [file-backed sessions](#file-backed-sessions), enabling [session archiving](serverconfig#archive-terminated-sessions) is a slightly more performant option.

## File-Backed Sessions

When a session directory (`--sessions` or equivalent from the GUI settings dialog) is set,
sessions will be stored in files instead of just kept in memory. This allows sessions to
survive server restarts and crashes. It also saves some memory, since only parts immediately
needed have to be kept in RAM.

When the server is shut down, active sessions are not deleted automatically, even if not
marked as persistent.

A session on disk consists of two or more files:

* `id.session` - session metadata
* `id (x).dprec` - session history

A new `dprec` is created each time the session is reset. If archive mode is enabled,
session files are never deleted. Instead, `.archived` is added to the end of the filename
when a session is terminated. This is a more efficient alternative to recording sessions.

## Session Templates

Session templates allow you to provide default sessions that always exist on the server.
Templates are looked for in the directory specified by the `--templates` command line parameter.

Template files are session recordings, either in binary (`.dprec`) or text (`.dptxt`) format. You can use the `dprectool` utility to convert binary to text recordings and vice-versa.

To create a template, set up a session to your liking and then use File → Export → Export Session Template. You'll probably want to save it as a .dptxt file so you can edit the header to add any of the following metadata:

* `version` (string) - protocol version (default=server's version)
* `title` (string) - session title
* `founder` (string) - name of the user who created the session
* `nsfm` (boolean) - content not suitable for minors (default=false)
* `preserveChat` (boolean) - include chat in session history (default=false)
* `persistent` (boolean) - persistent session (default=false)
* `deputies` (boolean) - whether trusted users have kick permission (default=false)
* `authOnly` (boolean) - allow only users with a registered account to join the session (default=false, since drawpile-srv 2.2.2)
* `idleOverride` (boolean) - disable the idle timeout for this session (default=false, since drawpile-srv 2.2.2)
* `allowWeb` (boolean) - allow users to join this session via web browser (default=false, since drawpile-srv 2.2.2)
* `maxUserCount` (integer) - maximum number of simultaneous users (default=25)
* `password` (string) - password hash (for a plaintext password, prefix it with `plain;`)
* `opword` (string) - operator password hash (dito about `plain;`)
* `announce` (string) - announce the session at this URL

Note: when using a `dptxt` template, the first two numbers in the server's protocol version must match those in the `version` header. For binary `dprec` templates, it's enough that the first number matches.

The name of the template file will be used as the session alias. Sessions created from the template still get unique IDs, but share the same alias.

## External Authentication

Ext-auth is a user authentication mechanism that delegates the actual authentication to
an external server. It is an easy way to integrate Drawpile login with a a website's
existing user account system. With ext-auth, the user's password is never sent
to the Drawpile server, so it is usable by untrusted 3rd party servers.

To enable ext-auth, three settings must be set on the server side:

1. The ext auth server URL (set with `--extauth` command line argument)
2. The `extauth` setting must be set to `true`
3. The `extauthkey` setting must be set to the server's validation key

If guest logins are enabled, the server will query the ext-auth server for guest
login permission. If guest logins are disabled, this
step is skipped. By default, if guest logins are enabled but the auth server is
unreachable, guest login will be permitted for all usernames not on the server's
built-in user list. (The built-in user list always takes precedence over ext-auth
users.) If guest logins should not be permitted, set `extauthfallback` to `false`.

The ext-auth server URL is sent to the client, which prompts the user for a password.
The password is then sent to the ext-auth server which will return a signed login token.
The client sends the login token back to the Drawpile server, which checks it using
the ext-auth validation key.

To enable logins using drawpile.net accounts on your server, use these settings:

* `--extauth https://drawpile.net/api/ext-auth/`
* `extauthkey = 9eJ2tMJlqgSqHOIK/GI/qzS14WqIxHeB1Im5Hs/CCCk=`

If you wish to implement your own authentication server, refer to [the ext-auth help page](/help/tech/extauth).

## Remote Admin

The server provides a HTTP API for remote administration. It is enabled via [the `--web-admin-port` command-line option](serverconfig#web-admin-listen-port). By default, only connections from localhost are accepted and no authentication is needed.

Use [the `--web-admin-access` command-line option](serverconfig#web-admin-access-mask) to grant access to the wider network and [the `DRAWPILE_SRV_WEB_ADMIN_AUTH` environment variable](serverconfig#web-admin-auth) to set a HTTP BASIC Auth username/password pair.

### Web Admin Interface

You probably want to use the remote admin API via the web admin interface. The following description assumes you're using nginx and know how to use it to a degree. Other web servers will also work fine.

You can grab the dpwebadmin frontend [from the GitHub releases](https://github.com/drawpile/dpwebadmin/releases). Unpack it, put the files into a directory you can serve and chown them to the appropriate user. If you want to use paths other than `/admin/` and `/admin/api`, you must build the frontend yourself.

Ideally, you create a credentials file. You can do this using the `htpasswd` tool. On Debian and Ubuntu, you can install this via `apt install apache2-utils`, other distributions are similar. Create the password file using `touch /etc/nginx/passwords`, then set credentials using `htpasswd /etc/nginx/passwords drawpileadmin` and entering a password. You can use a different file path or specify a different user than `drawpileadmin` at your choosing.

Then configure your nginx to serve those files and reverse proxy to drawpile-srv. Here's the general setup, fill in the rest like you would with any other site:

```nginx
server {
    # Other server configuration goes here.

    location /admin/ {
        auth_basic "Drawpile Server";
        auth_basic_user_file /etc/nginx/password;

        # Replace this with the path to where you put the frontend files.
        # Make sure this has a trailing slash!
        alias /path/to/webadmin/directory/;

        try_files $uri index.html =404;

        location /admin/api/ {
            # Port must match the one given ot the --web-admin-port option.
            proxy_pass http://localhost:27780/api/;
            proxy_redirect default;
        }
    }
}
```

### WebSocket Interface

To allow connecting to drawpile-srv from the web browser client, additional setup is required. There's [a dedicated page on this topic](websocket).
