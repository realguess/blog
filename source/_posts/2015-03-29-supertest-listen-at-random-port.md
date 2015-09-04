title: "SuperTest: Listen at Random Port"
date: 2015-03-29 12:00:00
categories: [JavaScript]
tags: [random, ephemeral, port, supertest, http, express, koa, node]
---

In the example section of [SuperTest] (_v0.15.0_), it mentions something about _ephemeral port_:

> You may pass an `http.Server`, or a `Function` to `request()` - if the server is not already listening for connections then it is bound to an ephemeral port for you so there is no need to keep track of ports. - https://github.com/visionmedia/supertest#example

Let's dissect this sentence. There are a few key concepts to grasp:

1. The `request` function will accept either a function or an `http.Server` object.
2. When does the server not listening for connections?
3. What is an ephemeral port?

Taking a peek at the `index.js` file in [SuperTest] source code, it is easy to see that it accepts both function and `http.Server` object, but prefer latter:

```js
// See `index.js` in [SuperTest] source code.
if ('function' == typeof app) app = http.createServer(app);

// See `lib/test.js` in [SuperTest] source code.
Test.prototype.serverAddress = function (app, path) {
  var addr = app.address();
  if (!addr) this._server = app.listen(0);
  var port = app.address().port;
  var protocol = app instanceof htts.Server ? 'https' : 'http';
  return protocol + '://1270.0.0.1:' + port + path;
});
```

Why it does this? This has to do with [Express]:

```js
> typeof require('http').createServer()
'object'
> typeof require('express')()
'function'
> typeof require('koa')()
'object'
> require('http').createServer(require('express')()).address()
null
```

Only when initiating an [Express] app, it returns a function, not an object. And follows up on `lib/test.js`, when [SuperTest] detects the created server is yet to bind to any port number, it will invoke `app.listen(0)`, so called _ephemeral port_. In fact, it is just a random port.

When something is _ephemeral_, it last for a very short time. When allowing a server to accept connections, we usually do is setting the server to listen on a specific port:

```js
app.listen(3001);
```

What if setting this to `0` like above or omit this port number?

<!-- more -->

> Begin accepting connections on the specified `port` and `hostname`. If the `hostname` is omitted, the server will accept connections on any IPv6 address (::) when IPv6 is available, or any IPv4 address (0.0.0.0) otherwise. **A port value of zero will assign a random port.** - https://iojs.org/api/http.html#http_server_listen_port_hostname_backlog_callback

If we omit the port number, it is treated as a value of zero. If fact, anything that evaluates to be _falsy_, including zero, will be assigned a random port. Hence the following are the same:

```js
app.listen();
app.listen(0);
app.listen(undefined);
app.listen(null);
app.listen(false);
app.listen('');
```

So, do not get confused by the word _ephemeral_. What [SuperTest] does for [Express] is that, if the server is yet to be created, it will create it for you and bind to a random port. And this only applies to [Express]. When we use [SuperTest] with [Express], we can simply do:

```js
var app = require('express')();
request(app)
```

But when we use [SuperTest] with [Koa], we have to create the server:

```js
var app = require('koa')();
var server = app.listen();
request(server)
```

The legacy of [Express] continues. That's why when using [Koa], we will do `app.listen()` instead.

Another thing to note that both [Express] and [Koa] instantiate an `http.Server` instance only when it invokes `listen` method to bind to a specific port number:

```js
> require('http').createServer() instanceof require('http').Server
true
> require('express')() instanceof require('http').Server
false
> require('koa')() instanceof require('http').Server
false
> require('express')().listen() instanceof require('http').Server
true
> require('koa')().listen() instanceof require('http').Server
true
```

There is a difference between the application created by [Express] and [Koa], and the server created by the application after invoking the `listen` method. And this is completely different from the core HTTP module. Creating an application in [Express] and [Koa] does not equal to creating a HTTP server instance.

I always thought that both `app.listen` will just return the same `app`, and `app` is an instance of `http.Server`, but it is not. In [Koa] and [Express], `app` is the application, `server` is the true HTTP server.


[SuperTest]: https://github.com/visionmedia/supertest
[Express]: http://expressjs.com/
[Koa]: http://koajs.com/
