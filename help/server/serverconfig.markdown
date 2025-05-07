---
layout: drawpile_help
title: "Server Configuration"
description: "Settings that affect drawpile-srv, Drawpile's dedicated server."
date: 2024-02-20 00:00:00 +0200
category: "help"
tag: help server
---

For more general information about drawpile-srv, take a look at [the dedicated server help page](dedicatedserver).

* Table of contents
{:toc}

## Startup Configuration

Startup configuration comes in the form of command-line arguments, environment variables and init system sockets. This is stuff you have to set when Drawpile starts and can't be modified while it's running.

The following lists the available configuration options, along with their default values.

### TCP Listen Port

Command-line option: `--port <port>`, `-p <port>`

Default value: `27750`

Which port to listen to for TCP connections. If you leave it at the default port, URLs will be shorter, since they can omit the port.

If you're passing sockets via systemd, this option is ignored.

### TCP Listen Address

Command-line option: `--listen <address>`, `-l <address>`

Default value: `0.0.0.0` (all addresses)

Which IP address the server should accept TCP connections from. Unless you have some special proxy configuration, you probably want to leave this at its default value.

If you're passing sockets via systemd, this option is ignored.

### WebSocket Listen Port

Command-line option: `--websocket-port <port>`

Default value: `0` (disabled)

Which port to listen to for WebSocket connections, if you want them. `27751` is the typical port for that. You need to [set up a reverse proxy](websocket#reverse-proxy) to actually make this work for the browser version of Drawpile.

If you're passing sockets via systemd, this option is ignored. If this option doesn't exist, you probably built drawpile-srv without having QtWebSockets available.

### WebSocket Listen Address

Command-line option: `--websocket-listen <address>`

Default value: `0.0.0.0` (all addresses)

Which IP address the server should accept WebSocket connections from. If your [reverse proxy](websocket#reverse-proxy) is on the same machine, you can set this to `127.0.0.1`.

If you're passing sockets via systemd, this option is ignored. If this option doesn't exist, you probably built drawpile-srv without having QtWebSockets available.

### Web Admin Listen Port

Command-line option: `--web-admin-port <port>`

Default value: `0` (disabled)

Which port to listen to for access to the web admin API, if you want to enable that. `27780` is the typical port for that.

If you're passing sockets via systemd, this option is ignored. If this option doesn't exist, you probably built drawpile-srv without having libmicrohttpd available.

### Web Admin Access Mask

Command-line option: `--web-admin-access <mask>`

Default value: `127.0.0.1` (localhost only)

Which IP address or subnet to allow access to the web admin API from. If you're using a reverse proxy on the same machine, you can set this to `127.0.0.1`.

If this option doesn't exist, you probably built drawpile-srv without having libmicrohttpd available.

### Web Admin Credentials

Environment variable: `DRAWPILESRV_WEB_ADMIN_AUTH=<user:password>`

Command-line option: `--web-admin-auth=<user:password>`

Default value: empty (no authentication)

The username and password to use to access the web admin API. Strongly recommended that you set this to proper values. The environment variable is preferred, since that won't show up in process listings like the command-line option does.

You can make sure that these credentials are working by running `curl http://localhost:27780/api/server`. If you get "401 - Unauthorized" back, it's working, if you get settings in JSON format, it's not.

If this option doesn't exist, you probably built drawpile-srv without having libmicrohttpd available.

### TLS Certificate

Command-line option: `--ssl-cert <certificate>`

Default value: empty (no TLS)

A path to the certificate file for TLS. If you're using Let's Encrypt, this should be pointed at to the `/etc/letsencrypt/live/YOURDOMAIN/fullchain.pem` - do *not* point it at `cert.pem`, that doesn't work properly. For some systems, such a misconfiguration may *appear* to work, but others will refuse to connect outright.

This only apples to TCP connections. WebSocket connections must use [a reverse proxy](#websocket-reverse-proxy-configuration) instead.

### TLS Key

Command-line option: `--ssl-key <key>`

Default value: empty (no TLS)

A path to the key file for TLS. If you're using Let's Encrypt, this should be pointed at to the `/etc/letsencrypt/live/YOURDOMAIN/privkey.pem`.

This only apples to TCP connections. WebSocket connections must use [a reverse proxy](#websocket-reverse-proxy-configuration) instead.

### TLS Key Algorithm

Command-line option: `--ssl-key-algorithm <algorithm>`

Default value: `guess`

Which encryption algorithm your TLS key uses. One of the following:

* `guess` to let drawpile-srv figure it out, which will detect RSA or ECDSA keys. This value should be fine in most cases.
* `rsa` for RSA, the former standard algorithm.
* `ec` for ECDSA, the current standard algorithm.
* `dsa` for DSA, rarely used and insecure algorithm.
* `dh` for Diffie-Hellman, even more rarely used algorithm.

This only apples to TCP connections. WebSocket connections must use [a reverse proxy](#websocket-reverse-proxy-configuration) instead.

### Configuration Database

Command-line option: `--database <path>`, `-d <path>`

Default value: empty (no configuration database)

Enables database configuration using the file under the given path. This will be an SQLite database, which typically uses `.db` as a file extension, although this is not required.

You can read more about the configuration database [on the dedicated server help page](dedicatedserver#configuration-database).

This option is mutually exclusive with the configuration file option.

### Configuration File

Command-line option: `--config <path>`, `-c <path>`

Default value: empty (no configuration file)

Enables file-based configuration using the file under the given path. The file is typically given a `.cfg` file extension, although this is not required.

You can read more about the configuration database [on the dedicated server help page](dedicatedserver#configuration-file).

This option is mutually exclusive with the configuration database option.

### Session Storage Directory

Command-line option: `--sessions <path>`, `-s <path>`

Default value: empty (file-backed sessions disabled)

Where to store the files for file-backed sessions. Must be pointed at an existent directory. Passing this option is a requirement for the [persistent sessions runtime configuration](#persistent-sessions) to have any effect.

### Session Templates Directory

Command-line option: `--templates <path>`, `-s <path>`

Default value: empty (no session templates)

Directory to source session templates from. For more information, see [the documentation on session templates](dedicatedserver#session-templates).

### Session Recording Directory

Command-line option: `--record <path>`

Default value: empty (don't record sessions)

Directory to store recordings of all sessions in. If you're using [file-backed sessions](#session-storage-directory), you can instead enable [session archiving](#archive-terminated-sessions), which is a little bit more performant.

### Hostname

Command-line option: `--local-host <hostname>`

Default value: empty (uses IP address)

The intended hostname for your server, if you have a domain name for it. Used for session announcements.

### Announcement Port

Command-line option: `--announce-port <port>`

Default value: the TCP listening port.

Which port to use for session listings. Only relevant if your external port differs from the port that drawpile-srv is listening on.

### External Authentication URL

Command-line option: `--extauth <url>`

Default value: empty (no external authentication)

The URL of the external authentication endpoint to use, for example drawpile.net's accounts. You also have to [enable external authentication](#external-authentication-enabled) and set the [external authentication validation key](#external-authentication-validation-key).

If you want to use drawpile.net accounts on your server, use `https://drawpile.net/api/ext-auth/`.

### Abuse Report URL

Command-line option: `--report-url <url>`

Default value: empty (no reporting)

The URL that gets hit when someone uses the "report" function in Drawpile. You also have to [enable abuse reporting](#abuse-report-enabled) and [set the abuse report auth token](#abuse-report-auth-token).

You can use [the abuse report relay](https://github.com/drawpile/abusereport/) to accept and forward these reports.

### Session Ban Encryption Key

Command-line option: `--crypt-key <key>`

Default value: empty (only moderators can export session bans)

A key to encrypt and decrypt session bans. This allows users to export and import session bans, which are encrypted because they contain IP addresses.

You can run `drawpile-srv --generate-crypt-key` to generate a key. If you change the key, previous exports will stop working, so do it only once.

## Runtime Configuration

Runtime configuration works either via a text-based config file or a database file. The former is edited by hand, the latter through the web admin interface. It can be changed without having to restart the server.

Configuration keys are **case-sensitive**. Values come as the following types:

* boolean: `true` or `false`.
* integer: a whole number.
* string: text of some sort.
* duration: either just a number describing seconds, or a number plus the suffix `s` for seconds, `m` for minutes, `h` for hours or `d` for days. The number may be fractional, so `1.5m` means 90 seconds. If the result includes fractional seconds, they will be truncated.
* size: either just a number describing bytes, or a number plus the suffix `b` for bytes, `kb` for kibibytes, `mb` for mebibytes or `gb` for gibibytes. The same rules for fractions as above apply.

When you use the web admin API to set these values, they are always provided as strings.

### Connection timeout

Key: `clientTimeout`

Default value: `60` (one minute)

Type: duration

Timeout for clients. If no message is received in this time, the client will be disconnected.

### Log retention

Key: `logpurgedays`

Default value: `0` (retain forever)

Type: integer

How many days to keep logs in the database around before deleting them. A value of zero retains them forever.

Only has an effect if you're providing [a database file](#configuration-database) on startup.

### Session size limit

Key: `sessionSizeLimit`

Default value: `99mb` (99 mebibytes)

Type: size

Maximum size of a session. Hitting this limit will stop the session history from growing any further, the user must reset it to a smaller size.

### Default autoreset threshold

Key: `autoResetThreshold`

Default value: `15mb` (15 mebibytes)

Type: size

The default value set for session autoresets. Users can adjust this up to [the maximum session size](#session-size-limit).

### Max simultaneous sessions

Key: `sessionCountLimit`

Default value: `25`

Type: integer

Maximum number of sessions that are allowed to be hosted simultaneously. An attempt to host another session when the limit is hit will result in the user being told that the server is full.

### Max users per session

Key: `sessionUserLimit`

Default value: `254`

Type: int

Maximum amount of users allowed in a single session. Moderators and admins can override this limit.

### Allow sessions to persist without users

Key: `persistence`

Default value: `false` (persistent sessions not allowed)

Type: boolean

Allows sessions to persist without users in them. If you enable this, you probably also want to set [an idle time limit](#idle-time-limit).

This option only has an effect if [a session storage directory](#session-storage-directory) is provided on startup.

### Archive terminated sessions

Key: `archive`

Default value: `false` (don't archive)

Type: boolean

When a file-backed session is reset or terminated, the files are not deleted, but instead renamed to have `.archived` extension.

This option only has an effect if [a session storage directory](#session-storage-directory) is provided on startup.

### Idle time limit

Key: `idleTimeLimit`

Default value: `0` (no idle timeout)

Type: duration

How long a session is allowed to stay up with no activity happening in it. Once this time lapses, the session will be terminated. If you [enable idle overrides](#allow-admins-and-moderators-to-disable-the-idle-timeout-for-individual-sessions), server moderators will be able to disable the time limit for individual sessions.

A value of zero will disable the idle timeout.

### Allow admins and moderators to disable the idle timeout for individual sessions

Key: `allowIdleOverride`

Default value: `true`

Type: bool

Whether to allow overriding of [the idle time limit](#idle-time-limit). This lets you designate sessions as "permanent" by disabling their timeout.

### Server title

Key: `serverTitle`

Default value: empty

Type: string

A title for your server, shown in the login dialog when the user connects to it. You can include a URL here if you want to have a page for more information or something.

### Welcome message

Key: `welcomeMessage`

Default value: empty

Type: string

A message to put into the chat when a user joins or hosts a session. Also known as "message of the day" or "MOTD".

### Server rules

Key: `ruleText`

Default value: empty

Type: string

The rules for your server. Keep this reasonably short, provide a link to a website instead. Users won't read a ginormous document inside the tiny login dialog window.

### Login info link

Key: `loginInfoUrl`

Default value: empty

Type: string

A link where users can register for an account on your server.

If you use drawpile.net's accounts, leave this blank, Drawpile will automatically show an appropriate link.

### Force all sessions to be marked Not Suitable for Minors (NSFM)

Key: `forceNsfm`

Default value: `false`

Type: bool

Forces every session to be tagged NSFM and doesn't allow users to remove this tag. If you're running an adults-only server, this setting should be enabled.

### Allow unauthenticated users

Key: `allowGuests`

Default value: `true` (unauthenticated users allowed)

Type: boolean

Whether to allow users that don't have an account (on your server or through external authentication) to connect to the server.

### Allow anyone to host

Key: `allowGuestHosts`

Default value: `true` (anyone can host sessions)

Type: boolean

Allows you to restrict session hosting to only accounts with the `HOST` flag.

### Allow anyone to join via web browser

Key: `allowGuestWeb`

Default value: `true`

Type: bool

This option decides if anyone is allowed to join via web browser or if only users with the WEB flag can.

Requires you to set [a WebSocket listen port](#websocket-listen-port) on startup and [set up a reverse proxy](#websocket-reverse-proxy-configuration) for it.

### Allow anyone to host via web browser

Key: `allowGuestWebHosts`

Default value: `true`

Type: bool

This option decides if anyone is allowed to host sessions via web browser or if only users with the WEBHOST flag can.

Requires you to set [a WebSocket listen port](#websocket-listen-port) on startup and [set up a reverse proxy](#websocket-reverse-proxy-configuration) for it.

### Allow anyone to manage web browser allowance on sessions

Key: `allowGuestWebSession`

Default value: `true`

Type: bool

Whether anyone is allowed to toggle the "allow joining via web browser" option for a session or if only users with the WEBSESSION flag are allowed to do so.

Requires you to set [a WebSocket listen port](#websocket-listen-port) on startup and [set up a reverse proxy](#websocket-reverse-proxy-configuration) for it.

### Allow moderator ghosts to join

Key: `enableGhosts`

Default value: `false`

Type: bool

Whether to allow moderators with the GHOST flag to join a session. These are used so that the moderator appears to be "the server", rather than startling users because someone they don't know joined their passworded session.

Ghosts are not truly hidden, them joining and leaving still shows up in the session event log, which any user can look at.

### Allow custom avatars

Key: `customAvatars`

Default value: `true`

Type: bool

Whether users are allowed to provide avatars. If not, they'll just get the auto-generated avatar that's a circle with the initial grapheme of their username.

### Minimum protocol version

Key: `minimumProtocolVersion`

Default value: empty

Type: string

Restrict sessions being hosted to the given protocol version or higher.

### Only allow joining sessions through direct links

Key: `mandatoryLookup`

Default value: `false`

Type: bool

Removes the ability to join the server and see a list of all sessions running on it. Instead, users can only join when using direct links, such as invite links or session listings.

Requires clients to use Drawpile 2.2 or newer. Older clients don't support the required operations, so they will be disconnected immediately and told to upgrade.

### Abuse report enabled

Key: `abusereport`

Default value: `false`

Type: bool

Whether to enable abuse reporting through the client.

Requires you to set [an abuse report URL](#abuse-report-url) on startup and [provide an abuse report auth token](#abuse-report-auth-token).

### Abuse report auth token

Key: `reporttoken`

Default value: empty

Type: string

Authentication token to send along with abuse report requests, to prevent random external connections from triggering your abuse report service.

Only has an effect if you set [an abuse report URL](#abuse-report-url) on startup and [enable abuse reports](#abuse-report-enabled).

### External authentication enabled

Key: `extauth`

Default value: `false` (disabled)

Type: boolean

Enables user authentication using external accounts. Requires you to provide [a URL for authentication](#external-authentication-url) on startup and [a validation key](#external-authentication-validation-key).

### External authentication validation key

Key: `extauthkey`

Default value: empty

Type: string

The key of the external authentication server. Only has an effect if you to provide [a URL for authentication](#external-authentication-url) on startup and [enable external authentication](#external-authentication-enabled).

If you want to use drawpile.net's authentication, use the key `9eJ2tMJlqgSqHOIK/GI/qzS14WqIxHeB1Im5Hs/CCCk=`

### External authentication user group

Key: `extauthgroup`

Default value: empty

Type: string

The group to use for external authentication. The use depends on the authentication server.

For drawpile.net authentication, this is only relevant for community servers, they get a group assigned to them so they can assign their own permissions.

### Permit guest logins when ext-auth server is unreachable

Key: `extauthfallback`

Default value: `true` (permitted)

Type: bool

Allows users to log in as guests when the external authentication is down.

### Allow ext-auth moderators

Key: `extauthmod`

Default value: `true`

Type: bool

Whether to pay mind to the external authentication MOD flag, turning users into moderators.

If you're using drawpile.net's authentication, you should turn this off unless you're running one of the official community servers.

### Allow ext-auth hosts

Key: `extauthhost`

Default value: `true`

Type: bool

Whether to pay mind to the external authentication HOST flag, allowing users to host sessions.

If you're using drawpile.net's authentication, you should turn this off unless you're running one of the official community servers.

### Allow ext-auth ban exemptions

Key: `extauthbanexempt`

Default value: `false`

Type: bool

Whether to pay mind to the external authentication's BANEXEMPT flag, which disables bans for certain users. This is supposed to be used to let people bypass broad IP bans.

If you're using drawpile.net's authentication, you should leave this off unless you're running one of the official community servers.

### Allow ext-auth ghosts

Key: `extauthghosts`

Default value: `false`

Type: bool

Whether to pay mind to the external authentication's GHOST flag.

If you're using drawpile.net's authentication, you should turn this off unless you're running one of the official community servers.

### Allow ext-auth web

Key: `extauthweb`

Default value: `false`

Type: bool

Whether to pay mind to the external authentication's WEB flag.

If you're using drawpile.net's authentication, you should turn this off unless you're running one of the official community servers.

### Allow ext-auth web session

Key: `extauthwebsession`

Default value: `false`

Type: bool

Whether to pay mind to the external authentication's WEBSESSION flag.

If you're using drawpile.net's authentication, you should turn this off unless you're running one of the official community servers.

### Allow ext-auth web host

Key: `extauthwebhost`

Default value: `false`

Type: bool

Whether to pay mind to the external authentication's WEBHOST flag.

If you're using drawpile.net's authentication, you should turn this off unless you're running one of the official community servers.

### Allow ext-auth persistence

Key: `extauthpersist`

Default value: `false`

Type: bool

Whether to pay mind to the external authentication's PERSIST flag.

If you're using drawpile.net's authentication, you should turn this off unless you're running one of the official community servers.

### Use ext-auth avatars

Key: `extAuthAvatars`

Default value: `true`

Type: bool

Whether to use avatars provided by the external authentication server.

If you're using drawpile.net's authentication, you should leave this on, since otherwise users will be confused why the avatar they set on the website doesn't show up on your server.

### Use list server URL whitelist

Key: `announceWhitelist`

Default value: `false`

Type: boolean

Whether to restrict session listings to only explicitly allowed listing servers. The list of allowed servers is provided separately, this option only toggles if it is used or not.

### External bans source URL

Key: `extBansUrl`

Default value: empty

Type: string

DA URL of a a ban list service.

Drawpile's community servers are given a URL like this so that they can share a single ban list. If you're running your own server, this is probably unnecessary, since you can just enter the bans into the server itself.

### External bans check interval

Key: `extBansCheckInterval`

Default value: `900` (15 minutes)

Type: time

How often to check the external ban service.

### Password-dependent browser allowance

Key: `passwordDependentWebSession`

Default value: `false`

Type: boolean

Whether to automatically allow joining passworded sessions via web browser and disallow joining public ones.

### Empty session linger time

Key: `emptySessionLingerTime`

Default value: `0`

Type: time

How long non-persistent, empty sessions should linger after the last user left. Consider setting this to at least be a few minutes to let users rejoin if they accidentally got disconnected when they were the only person in a session.

If you've passed [a session storage directory via --session](#session-storage-directory), sessions will also persist across restarts of the server for this amount of time. This can make the server going down much less disruptive, since users can just reconnect and keep drawing.

## Require matching host

Key: `requireMatchingHost`

Default value: `false`

Type: boolean

Require hostnames given by clients to match the value given via `--local-host`. Enabling this option restricts clients to version 2.2.2 or newer, since older versions did not pass any hostnames.

## Enable invite codes by default

Key: `invites`

Default value: `true`

Type: boolean

Whether to enable invite codes on newly hosted sessions. Moderators or server administrators can always toggle this per session.

## Prefer WebSockets

Key: `preferWebSockets`

Default value: `false`

Type: boolean

Whether the server prefers using WebSockets over TCP sockets. This preference gets communicated to clients, which will create appropriate invite links for it that point at the WebSocket endpoint. Clients older than version 2.2.2 disregard the preference and always generate TCP invite links.

WebSockets require passing [a WebSocket listen port](#websocket-listen-port) on startup and [set up a reverse proxy](#websocket-reverse-proxy-configuration) for it.
