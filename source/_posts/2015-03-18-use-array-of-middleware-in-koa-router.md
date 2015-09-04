title: Use Array of Middleware in Koa Router
date: 2015-03-18 12:00:00
categories: [JavaScript]
tags: [koa-router, koa-compose, koa, middleware, array, node]
---

[Koa Router] _v4.2_ does not support an array of middleware, multiple middleware must be entered one by one:

```js
app.get('/foo', middleware1, middleware2);
```

Using an array is much cleaner than multiple arguments.

```js
app.get('/foo', [middleware1, middleware2]);
```

But the above will throw error:

```sh
Error: GET: `middleware` must be a function, not `object`
```

This can be easily fixed by using [Koa Compose]:

```js
var app = require('koa')();
var router = require('koa-router');
var compose = require('koa-compose');

app.use(router(app));

app.get('/foo', compose([
  function *(next) {
    this.body = {};
    yield next;
  },
  function *(next) {
    this.body.foo = 'foo';
    yield next;
  },
  function *(next) {
    this.body.bar = 'bar';
    yield next;
  }
]));
```

Hence:

```js
app.get('/foo', compose([middleware1, middleware2]));
```

[Koa Router]: https://github.com/alexmingoia/koa-router
[Koa Compose]: https://github.com/koajs/compose
