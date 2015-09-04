title: Node.js HTTP Server One Liner
date: 2015-09-04 12:00:00
categories: [JavaScript]
tags: [starter, one-liner, server, http, node]
---

One liner to create a [Node.js] HTTP server:

```js
require('http').createServer(function (req, res) { res.end('OK'); }).listen(3000);
```

Print out the process ID instead:

```js
require('http').createServer(function (req, res) { res.end('' + process.pid); }).listen(3000);
```

This is useful with the worker process when implementing the [cluster] module.

[Node.js]: https://nodejs.org/
[cluster]: https://nodejs.org/api/cluster.html
