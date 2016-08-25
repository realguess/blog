title: Installing Caddy 0.9.x on Ubuntu/Debian System
date: 2016-08-25 12:00:00
categories: [DevOps]
tags: [install, caddy, web-server, x-content-type-options, range, accept-ranges, ulimit]
---

Install [Caddy] via its [installer script] on Ubuntu/Debian system:

```
$ curl -s https://getcaddy.com/ | sudo bash
Downloading Caddy for linux/amd64...
https://caddyserver.com/download/build?os=linux&arch=amd64&arm=&features=
Extracting...
Putting caddy in /usr/local/bin (may require password)
Caddy 0.9.1 (+e8e5595)
Successfully installed
```

This is different from the [Download] page, where you get to select additional features (see the `&features=` URL query parameter).

```
$ which caddy
caddy is /usr/local/bin/caddy
```

Get the installed version:

```
$ caddy --version
Caddy 0.9.1 (+e8e5595)
```

Get help:

```
$ caddy -h
Usage of caddy:
  -agree
        Agree to the CA's Subscriber Agreement
  -ca string
        URL to certificate authority's ACME server directory (default "https://acme-v01.api.letsencrypt.org/directory")
  -conf string
        Caddyfile to load (default "Caddyfile")
  -cpu string
        CPU cap (default "100%")
  -email string
        Default ACME CA account email address
  -grace duration
        Maximum duration of graceful shutdown (default 5s)
  -host string
        Default host
  -http2
        Use HTTP/2 (default true)
  -log string
        Process log file
  -pidfile string
        Path to write pid file
  -plugins
        List installed plugins
  -port string
        Default port (default "2015")
  -quic
        Use experimental QUIC
  -quiet
        Quiet mode (no initialization output)
  -revoke string
        Hostname for which to revoke the certificate
  -root string
        Root path of default site (default ".")
  -type string
        Type of server to run (default "http")
  -version
        Show version
```

Run Caddy locally:

```
$ caddy
Activating privacy features... done.
http://:2015
WARNING: File descriptor limit 1024 is too low for production servers. At least 8192 is recommended. Fix with "ulimit -n 8192".
```

A file descriptor is simply a number that the operating system assigns to an open file to keep track of it. Caddy's primary goal is to be an easy-to-use static file web server. Having high file descriptor limit means it can open more files to serve users at the same time.

```
$ ulimit -Sn && ulimit -Hn
1024
4096
```

The current system is too low in both soft and hard limits. But since it's not in production, warning can be ignored.

Make sure the server working:

```
$ http :2015
HTTP/1.1 404 Not Found
Content-Length: 14
Content-Type: text/plain; charset=utf-8
Server: Caddy
X-Content-Type-Options: nosniff

404 Not Found
```

Response header `X-Content-Type-Options: nosniff` prevents MIME based attacks, it tells the browser to respect the response content type, not to override.

Status code `404` means working, but just lacks an index file. Let's create one:

<!-- more -->

```
$ echo 'Hello Caddy' > index.html
```

Make the request again:

```
$ http :2015
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 12
Content-Type: text/html; charset=utf-8
Etag: W/"57bc0bdf-c"
Server: Caddy

Hello Caddy
```

The response header `Accept-Ranges` indicates that Caddy is capable of supporting range requests (part of the entire response entity):

```
$ http :2015 Range:bytes=5-11
HTTP/1.1 206 Partial Content
Accept-Ranges: bytes
Content-Length: 7
Content-Range: bytes 5-11/12
Content-Type: text/html; charset=utf-8
Etag: W/"57beadf7-c"
Server: Caddy

Caddy
```

> The "Range" (request) header field on a GET request modifies the method semantics to request transfer of only one or more subranges of the selected representation data, rather than the entire selected representation data.[^1]

Looking good!


[^1]: [3.1. Range](https://tools.ietf.org/html/rfc7233#section-3.1), Hypertext Transfer Protocol (HTTP/1.1): Range Requests
[Caddy]: https://caddyserver.com/
[installer script]: https://getcaddy.com/
[Download]: https://caddyserver.com/download
