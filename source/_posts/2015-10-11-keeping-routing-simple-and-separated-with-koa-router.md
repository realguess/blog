title: Keeping Routing Simple and Separated with Koa Router
date: 2015-10-11 12:00:00
categories: [JavaScript]
tags: [routing, koa-router, koa]
---

Debugging an error when using [koa-router] module that multiple routes were invoked on a single HTTP request:

```js
var app = require('koa')();
var router = require('koa-router')();

router.use(function *(next) {
  this.body = {};
  yield next;
});

router.get('/users', function *(next) {
  this.body['/users'] = true;
  yield next;
});

router.get('/users/:id?', function *(next) {
  this.body['/users/:id?'] = true;
  yield next;
});

app.use(router.routes());
app.listen(3000);
```

When making a request to get a list of users, it will match both routes:

```sh
$ http :3000/users
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 34
Content-Type: application/json; charset=utf-8

{
    "/users": true,
    "/users/:id?": true
}
```

Because in the second route `:id` is optional, and since there is `yield next` statement in the first route, the second route will be executed subsequently.

The cause of the bug is the design of the routing `GET /users/:id?`. The correct routes are:

- `GET /users`: Retrieve a list (array) of users.
- `GET /users/:id`: Retrieve a single user object.

The question mark should not be appended to the end of `id` parameter, which effectively marking the route to perform both duties. It's better to **keep them simple and keep them separated**.


[koa-router]: https://github.com/alexmingoia/koa-router
