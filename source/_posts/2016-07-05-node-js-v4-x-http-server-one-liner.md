title: Node.js v4.x HTTP Server One Liner
date: 2016-07-05 12:00:00  
categories: [Coding]  
tags: [one-liner, starter, server, http, node]
---

One liner to create a [Node.js] HTTP server:

```
require('http').createServer((req, res) => res.end('OK')).listen(3000);
```

Also see the [previous one liners][1] for older Node.js versions.


[1]: https://realguess.net/2015/09/04/node-js-http-server-one-liner/
[Node.js]: https://nodejs.org/
