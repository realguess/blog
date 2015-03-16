title: Use PM2 with io.js
date: 2015-03-14 12:00:00
categories: [Node.js and io.js]
tags: [pm2, iojs]
---

If you have both [Node.js] and [io.js] installed, [PM2] will run Node.js by default. To start the app with io.js, use `--interpreter` flag:

```txt
$ pm2 start app.js --name web --interpreter iojs
[PM2][WARN] We've notice that you are running the node script, but currently
using a iojs interpreter, may be you need inspect the --interpreter option.
[PM2] Process app.js launched
┌──────────┬────┬──────┬──────┬────────┬─────────┬────────┬─────────────┬──────────┐
│ App name │ id │ mode │ pid  │ status │ restart │ uptime │ memory      │ watching │
├──────────┼────┼──────┼──────┼────────┼─────────┼────────┼─────────────┼──────────┤
│ web      │ 2  │ fork │ 5074 │ online │ 0       │ 0s     │ 20.172 MB   │ disabled │
└──────────┴────┴──────┴──────┴────────┴─────────┴────────┴─────────────┴──────────┘
```

[Node.js]: https://nodejs.org/
[io.js]: https://iojs.org/
[PM2]: https://github.com/Unitech/PM2
