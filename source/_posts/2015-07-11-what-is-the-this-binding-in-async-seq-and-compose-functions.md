title: "What is the 'this' Binding in Async 'seq' and 'compose' Functions?"
date: 2015-07-11 12:00:00
categories: [Node.js and io.js]
tags: [this, binding, async, seq, compose, node]
---

TLDR: `global`

<!-- more -->

[Async] is a powerful utility module for working with asynchronous programming style in JavaScript. In the documentation, there are a couple places talking about [this]:

> Each function is executed with the `this` binding of the composed function. - [async]

This affects these two functions:

 - `seq(fn1, fn2...)`
 - `compose(fn1, fn2...)`

When printing the `this` context from a composed function, this is what we have:

```sh
[ 'global',
  'process',
  'GLOBAL',
  'root',
  'Buffer',
  'setTimeout',
  'setInterval',
  'clearTimeout',
  'clearInterval',
  'setImmediate',
  'clearImmediate',
  'console' ]
```

This looks very much like the [global] namespace object.

Let's verify it:

```js
// Async: This Binding
// ===================
//
// Find out the `this` binding in Async composed functions.

'use strict';


// Dependencies
// ------------
var async = require('async');


// Functions
// ---------
function fn1(done) {
  console.log('fn1: this === global ?', this === global);

  setTimeout(function () {
    done();
  }, 10);
}

function fn2(done) {
  console.log('fn2: this === global ?', this === global);

  setTimeout(function () {
    done();
  }, 10);
}


// Main
// ----
var fns = async.seq(fn1, fn2);
fns(function (err) {
  if (err) {
    throw err;
  }
});
```

The result:

```sh
fn1: this === global ? true
fn2: this === global ? true
```

This is due to the behavior of `this` in Node.js:

```js
console.log(this === global);  // false
console.log(this === exports); // true
(function(){ console.log(this === global); }());  // true      
(function(){ console.log(this === exports); }()); // false
```

[this]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this
[global]: https://nodejs.org/api/globals.html#globals_global
[async]: https://github.com/caolan/async