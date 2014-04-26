title: Share JavaScript Code between Browser (front-end) and Node (back-end)
date: 2013-06-29 09:11:46
tags: [javascript, node, browser, sharing, underscore, coffeescript]
---
One of the benefits coding in JavaScript is the ability to share code between
browser (front-end) and [Node][] (back-end) environments.

To share code between two different environment, first need to understand what
are specific to browser and what are specific to Node. Shared code should be
agnostic to both environments.

In browser environment, there is an obvious one: `window` object. In Node
environment, there are a few objects that are not globally defined in browser:
`global`, `process`, `module`, `exports` and more, see [global objects][]. All
these variables work, but `exports` is preferred.

```javascript
console.log('Hi ' + (typeof window !== 'undefined' ? 'browser!' : 'Node!'));
```

Once environment is identified, then it is just the matter of writing style,
either browser style or Node style.

For example, writing in Node style by passing the `exports` as the parameter:

```javascript
(function (exports) {
  var foo = exports.foo = {};

  // Node style code goes here.
}(typeof exports === 'undefined' ? this : exports));
```

By using browser style, here is an example from the [annotated source code][] of
[Underscore][]:

```javascript
// Export the Underscore object for Node.js, with backwards-compatibility
// for the old require() API. If we're in the browser, add _ as a global
// object via a string identifier, for Closure Compiler "advanced" mode.
if (typeof exports !== 'undefined') {
  if (typeof module !== 'undefined' && module.exports) {
    exports = module.exports = _;
  }
  exports._ = _;
} else {
  root._ = _;
}
```

To make it easier without concerning the backward compatibility:

```javascript
(function () {
  var foo = {};

  if (typeof exports !== 'undefined') {
    exports.foo = foo;
  } else {
    this.foo = foo;
  }

  // Browser style code goes here.
}).call(this);
```

`this` is the same as `window` object in the browser environment.

```javascript
(function (window) {
  // Browser style code goes here.
}(this));
```

One more thing, writing in [CoffeeScript][] is even better, because the compiled
code is already wrapped in closure, fewer indentation is needed.

```coffeescript
foo = {}
if exports isnt 'undefined'
  exports.foo = foo
else
  this.foo = foo

# Browser style code goes here.
```

Compile to JavaScript by [CoffeeScript][] 1.6.3:

```javascript
(function() {
  var foo;

  foo = {};

  if (exports !== 'undefined') {
    exports.foo = foo;
  } else {
    this.foo = foo;
  }

}).call(this);
```

Everything will be encapsulated inside the `foo` object.

[node]: http://nodejs.org/
[global objects]: http://nodejs.org/api/globals.html
[underscore]: http://underscorejs.org/
[annotated source code]: http://underscorejs.org/docs/underscore.html
[coffeescript]: coffeescript.org
