title: Re-assembling the Requested URL
date: 2015-10-02 12:00:00
categories: [JavaScript]
tags: [url, host, port, protocol, koa]
---

It is simple to know the request URL path, it's one (`this.url` in [Koa]) used by routing, but if you are looking to get the complete URL, it's a little bit complicated. Luckily, there is a quick way to re-assemble the requested URL in [Koa], if proxy is not a concern:

```js
var url = this.request.protocol + '://' + this.request.host + this.request.originalUrl;
```

Or a shorter version:

```
var url = this.protocol + '://' + this.host + this.originalUrl;
```

The port number is included in `this.host`, for example: `localhost:3000`.

Putting into a complete script:

```js
require('koa')().use(function *() {
  // Echo back the request URL.
  this.body = this.protocol + '://' + this.host + this.originalUrl;
}).listen(3000);
```

Assume that you have a server running on the host `www.example.com` with SSL enabled. If you make a request to the server with the URL: `https://www.example.com/path`, this URL will be echoed back in the response by the above script.

[Koa]: http://koajs.com/
