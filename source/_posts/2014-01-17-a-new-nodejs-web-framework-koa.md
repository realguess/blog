title: A New NodeJS Web Framework - Koa
date: 2014-01-17 14:13:03
tags: [koa, express, web-framework, node, yield, ecmascript-harmony]
---

Taking the first spin on [Koa][], a new web framework designed by the team behind [Express][].


Requirements
------------

1. Node v0.11.9 or higher is required for generator support.
2. `--harmony` flag must be used when running Node.

Without the `--harmony` flag, genertor function cannot be defined:

    function *fn() {
               ^
    SyntaxError: Unexpected token *
        at exports.runInThisContext (vm.js:69:16)
        at Module._compile (module.js:432:25)
        at Object.Module._extensions..js (module.js:467:10)
        at Module.load (module.js:349:32)
        at Function.Module._load (module.js:305:12)
        at Function.Module.runMain (module.js:490:10)
        at startup (node.js:123:16)
        at node.js:1031:3

So, set the `--harmony` flag in shell alias:

    $ alias node='node --harmony'

To know more:

    $ man node
    --harmony (enable all harmony features (except typeof))
          type: bool  default: false

_Harmony_ is the codename of ECMAScript 6, the next version of the standard.


Install
-------

    $ npm install koa

The footprint of the framework is small. Therefore, there is no executable binary like Express, and we do not need to install it globally.


Dependencies
------------

Dependencies on version 0.2.1:

    "dependencies": {
      "accepts": "~1.0.0",
      "type-is": "~1.0.0",
      "on-socket-error": "~1.0.1",
      "co": "~3.0.1",
      "debug": "*",
      "mime": "~1.2.11",
      "fresh": "~0.2.0",
      "koa-compose": "~2.1.0",
      "cookies": "~0.3.7",
      "keygrip": "~1.0.0"
    }


Middleware
----------

Middleware system is the core of either Express or Koa based application. This needs a separate post. Here just describes the difference in design.

In a simple assembly design, each unit passes control to the next one until the last one terminates and responses to the request:

        +-----+     +-----+     +-----+
    --> |     | --> |     | --> |     | --o
        +-----+     +-----+     +-----+

Express middleware passes control to the next one like an assembly line, but each unit is capable of responding to the request:

           o           o           o
           |           |           |
        +-----+     +-----+     +-----+
    --> |     | --> |     | --> |     |
        +-----+     +-----+     +-----+

Koa provides a traceback via generator, each unit is capable of working on the request twice instead of once as previous two designs. It is stack alike, where the first generator will also be the last one prior to responding to request:

           o
           |
        +-----+     +-----+     +-----+
    --> |     | --> |     | --> |     |
        +-----+     +-----+     +-----+
           ^          | ^         |
           +----------+ +---------+

This is sort of an assembly loop, where the starting station is also be the ending station.


Usage
-----

Here is one liner:

    require('koa')().use(function *() { this.body = 'OK'; }).listen(3000);

`app.use` requires a generator function or `function *()`. Koa middleware uses `this` (context) instead of `req` and `res` in Express. It also encapsulates Node's `request` and `response` objects. For example, `this` or context has the following properties:

    [ 'request',     // Koa request
      'response',    // Koa response
      'app',
      'req',         // Node request
      'res',         // Node response
      'onerror',
      'originalUrl',
      'cookies',
      'accept' ]


### Status and Body

Status can be used as a flag indicating whether a request has been processed or not:

    app.use(function *() {
      console.log(this.status); // undefined
      this.body = 'processed';
      console.log(this.status); // 200
    });

When the `body` is populated, `status` is automatically updated, probably via the `nextTick` method. So, we can use it as the flag to design a response such as a custom not found response.


### Not Found

Create a custom `Not Found` in JSON content type:

    app.use(function *(next) {
      yield next; // The operation is yet to start.
      if (this.status) { return; }

      this.status = 404;
      this.body = { message: 'Cannot find what you are looking for :(' };
    });
    app.use(function *() {}); // Do nothing to simulate not found.


### Error Handling

Koa app is capable of catching the error event:

    app.on('error', function (err, ctx) {
      // Log errors.
    });

where `ctx` is a context instance. But you cannot modify the response body. This is more for server side logging and debugging purpose. The `500 Internal Server Error` will be returned to the client.

We can use `try..catch` to define a custom error handling middleware:

    app.use(function *(next) {
      try {
        yield next;
      } catch (err) {
        this.status = err.status || 500;
        this.body   = { message: err.message || 'Bad stuff happened!' };
        this.app.emit('error', err, this); // Enable error event handling
      }
    });

Make sure to emit the error event and let the server log the error.

All non-operational middleware (e.g., not found or error) should be defined first, so it can be traced back as the last guards before sending response back.


### Routing

Out of the box, Koa does not support routing, but it can be achieved via routing middleware, see [examples][middleware].


Examples
--------

Define an app with only one middleware generator function:

```javascript
function *foo(next) {
  console.log('foo');
  this.status = 204;
}
require('koa')().use(foo).listen(3000);
```

Instead of using status code `200`, we use `204` here, because the response contains no body. If using `200`, the server does not know the total content length, will result in `Transfer-Encoding: chunked` header instead of `Content-Length`. See [chunked transfer encoding](http://en.wikipedia.org/wiki/Chunked_transfer_encoding).

This app simply prints out `foo` on the server side, and respond to a client with status code `204` and an empty body. We can even omit the `next` parameter, since we are not using it.

Now let's use two middleware generator functions:

```javascript
// Response: foo
function *foo(next) {
  this.body = 'foo';
}
function *bar(next) {
  this.body = 'bar';
}
require('koa')().use(foo).use(bar).listen(3000);
```

Since generator `foo` sets the body, generator `bar` will never be reached. We can change that by adding the `yield`: 

```javascript
// Response: bar
function *foo(next) {
  this.body = 'foo';
  yield next;
}
function *bar(next) {
  this.body = 'bar';
  yield next;
}
require('koa')().use(foo).use(bar).listen(3000);
```

This looks alot like Express, `yeild next` is similar to `next()`. Request travels through the middleware system first via `foo` then `bar`. But actually by using generator, it also returns back to `foo` as `foo->bar->foo`:

```javascript
// Response: foo again
function *foo(next) {
  this.body = 'foo';
  yield next;
  this.body = 'foo again';
}
function *bar(next) {
  this.body = 'bar';
  yield next;
}
require('koa')().use(foo).use(bar).listen(3000);
```

This is similar to a stack alike request model, where the first middleware should also be the last middleware to touch the request. There is a no-op middleware in the end to catch the last `yield next` produced by `bar` middleware generator and traces back the stack.


Debugging
---------

Set environment variable `DEBUG=koa*` to enable Koa specific debugging information in development environment:

    $ DEBUG=koa* node app.js

You will see a list of middleware. If a middleware is an anonymous function, we can set `._name` to define a custom name:

    function *foo(next) {
      // Middleware definition starts here.
    }
    foo._name = 'bar';
    app.use(foo);

The middleware name will be `bar` instead of `foo`.


Resources
---------

1. [Koa][]
2. [Koa | GitHub](https://github.com/koajs/koa)
3. [Koa Quick Guide](https://github.com/koajs/koa/blob/master/docs/guide.md)
4. [Koa Middleware List][middleware]

[koa]: http://koajs.com/
[middleware]: https://github.com/koajs/koa/wiki
[express]: http://expressjs.com/
