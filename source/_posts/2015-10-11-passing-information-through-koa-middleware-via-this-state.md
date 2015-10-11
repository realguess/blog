title: 'Passing Information Through Koa Middleware Via "this.state"'
date: 2015-10-11 12:00:00
categories: [JavaScript]
tags: [state, context, middleware, koa]
---

[Koa] has a [context] object created during every request/response cycle. The context is referenced via `this` in the middleware. Much information are captured in the context, such as `req` and `res`. In order to avoid polluting the context and colliding with other middleware, the information from your application should be encapsulated into `this.state` property, a namespace for passing information through middleware during request/response cycle:

```js
require('koa')().use(function *(next) {
  var state = this.state;

  state.user = yield users.create(this.request.body);
  state.code = 201;
  state.body = {
    name: user.name,
    mail: user.mail
  };

  yield next;
}).listen(3000);
```

The `this.state` has been initialized into an empty object:

```js
require('koa')().use(function *(next) {
  this.body = this.state;
}).listen(3000);
```

Therefore, we can start using it right away without doing our own initialization:

```sh
$ http :3000
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 2
Content-Type: application/json; charset=utf-8

{}
```

[Koa]: http://koajs.com/
[context]: http://koajs.com/#context
