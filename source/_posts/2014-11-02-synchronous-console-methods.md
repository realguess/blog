title: Synchronous Console Methods
date: 2014-11-02 12:00:00
categories: [Programming]
tags: [synchronous, asynchronous, console, log, node]
---

In Node v0.10.x, _the console functions are synchronous when the destination is a terminal or a file (to avoid lost messages in case of premature exit) and asynchronous when it's a pipe (to avoid blocking for long periods of time)._ See [console]. We can test it with the following script:

<!-- more -->

```
// Usage: `$ time node console.js`

var msg = '0';
var start;

for (var i = 0, len = 20; i < len; i+=1) {
  msg += msg;
}

process.on('exit', function (code) {
  console.log('Time elapsed: %d ms', Date.now() - start);
  console.log('Node', process.version);
});

start = Date.now();
console.log(msg);
console.log('Time elapsed: %d ms', Date.now() - start);
```

Run the script:

```
$ time node console.js
Time elapsed: 2966 ms
Time elapsed: 2967 ms
Node v0.10.33

real    0m3.017s
user    0m0.052s
sys     0m0.000s
```

Switching to other methods such as `console.error`, the result will still be synchronous, as stated in API documentation.

However, if you are running Node v0.11.x branch, things are different:

```
$ time node console.log
Time elapsed: 35 ms
Time elapsed: 2732 ms
Node v0.11.14

real    0m2.762s
user    0m0.040s
sys     0m0.012s
```

:)

[console]: http://nodejs.org/api/console.html
