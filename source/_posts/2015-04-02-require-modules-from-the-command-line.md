title: Require Modules from the Command Line
date: 2015-04-02 12:00:00
categories: [JavaScript]
tags: [require, module, cli, iojs]
---

[io.js] v1.6.0 introduces a new command line option: `-r` or `--require`, which can be used to pre-load modules at startup, see:

https://github.com/iojs/io.js/blob/v1.x/CHANGELOG.md#2015-03-19-version-160-chrisdickinson

I can think of one interesting use case: overriding the default behavior or `console.log` to perform deep inspection:

```js
// console.js
var util = require('util');
var log = console.log;

console.log = function (obj) {
  log(util.inspect(obj, { colors: true, depth: null }));
};
```

Now I can pre-load this script to have all console log show in better format:

```sh
$ iojs -r /path/to/console.js app.js
```

Time to find out more use cases.

[io.js]: https://iojs.org/
