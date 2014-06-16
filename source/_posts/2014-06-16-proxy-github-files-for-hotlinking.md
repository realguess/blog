title: Proxy GitHub Files for Hotlinking
date: 2014-06-16 14:13:29
tags: [hotlinking, rawgit, github, content-type, cors]
---

I have a library that I would like to try in [JSFiddle], for example, [Marked], a markdown parser and compiler. But I cannot by hotlinking the [minified JavaScript file] directly:

```html
<script src="https://raw.githubusercontent.com/chjj/marked/master/marked.min.js"></script> 
```

Error:

_Refused to execute script from 'https://raw.githubusercontent.com/chjj/marked/master/marked.min.js' because its MIME type ('text/plain') is not executable, and strict MIME type checking is enabled._

Double check the headers:

```
$ curl -I https://raw.githubusercontent.com/chjj/marked/master/marked.min.js
HTTP/1.1 200 OK
Server: Apache
Content-Security-Policy: default-src 'none'
Access-Control-Allow-Origin: https://render.githubusercontent.com
X-XSS-Protection: 1; mode=block
X-Frame-Options: deny
X-Content-Type-Options: nosniff
Strict-Transport-Security: max-age=31536000
Content-Type: text/plain; charset=utf-8
Cache-Control: max-age=300
Content-Length: 19160
Accept-Ranges: bytes
Via: 1.1 varnish
X-Served-By: cache-lax1429-LAX
X-Cache: MISS
X-Cache-Hits: 0
Vary: Authorization,Accept-Encoding
Source-Age: 0
```

The two headers that prevents hotlinking:

- `Content-Type` is set to `text/plain`, we are expecting `application/javascript`.
- `Access-Control-Allow-Origin` is not set to `*`, which prevents AJAX request.

The solution is to proxy the file by running your own server, or use [RawGit]:

```
$ curl -I https://rawgit.com/chjj/marked/master/marked.min.js
HTTP/1.1 200 OK
Server: nginx
Content-Type: application/javascript; charset=utf-8
Connection: keep-alive
Vary: Accept-Encoding
X-Content-Type-Options: nosniff
X-Robots-Tag: none
RawGit-Naughtiness: 0
Access-Control-Allow-Origin: *
Cache-Control: max-age=300
Vary: Accept-Encoding
RawGit-Cache-Status: MISS
```

The `Content-Type` has been updated to `application/javascript` and the CORS restricted has been relaxed via `Access-Control-Allow-Origin`.

Problem sovled and the fiddle works as expect:

<iframe width="100%" height="300" src="http://jsfiddle.net/realguess/2dmG5/embedded/html,result,js/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

[JSFiddle]: http://jsfiddle.net/
[Marked]: https://github.com/chjj/marked
[minified JavaScript file]: https://raw.githubusercontent.com/chjj/marked/master/marked.min.js
[RawGit]: https://rawgit.com/faq
