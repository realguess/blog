title: Locate HTTP Port Number from http.Server Instance
date: 2015-03-28 12:00:00
categories: [Node.js and io.js]
tags: [random, port, http, server, express, koa]
---

This is a bare minimal HTTP server:

```js
require('http').createServer().listen(port);
```

If the `port` number is evaluated to be _falsy_, such as `0`, `null`, `undefined` or an empty string, a random port will be assigned.

> Begin accepting connections on the specified `port` and `hostname`. If the `hostname` is omitted, the server will accept connections on any IPv6 address (::) when IPv6 is available, or any IPv4 address (0.0.0.0) otherwise. **A port value of zero will assign a random port.** - https://iojs.org/api/http.html#http_server_listen_port_hostname_backlog_callback

We can use command line tool such as `netstat` to find out the port number:

```sh
$ netstat -ltpn | grep node
tcp6     0    0 :::44055             :::*            LISTEN      5624/node
```

However, it will be very convenient without relying on external tools.

Luckily, we can find out the port number by using the `address` method:

```js
require('http').createServer().listen().address()
```

The result will be similar to:

```js
{ address: '::', family: 'IPv6', port: 44055 }
```

Furthermore, it works with all popular libraries that create an `http.Server` instance:

<!-- more -->

```js
> require('http').createServer().listen().address()
> require('express')().listen().address()
> require('koa')().listen().address()
```

They are all having the same result.

Finally, here is a [Koa] implementation that shows the port number the server is running at:

```js
var app = require('koa')();

app.listen(function () {
  console.log('Server is running at port ' + this.address().port);
});
```

[Koa]: http://koajs.com/
