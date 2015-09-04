title: Run LoDash in Context to Change Template Settings
date: 2015-07-22 12:00:00
categories: [JavaScript]
tags: [lodash, template, context, module-caching, node]
---

[LoDash] has `_.template` function:

> Creates a compiled template function that can interpolate data properties in "interpolate" delimiters, HTML-escape interpolated data properties in "escape" delimiters, and execute JavaScript in "evaluate" delimiters. Data properties may be accessed as free variables in the template. If a setting object is provided it takes precedence over _.templateSettings values.

The template settings affect the behavior of [LoDash], and due to Node's [module caching], this will affect the entire application. This will likely cause unexpected error, especially you are writing a module or a library, and the application that requires the module will also use LoDash but with a different template setting:

```sh
SyntaxError: Unexpected token =
```

The rule of thumb: **Don't do it!**

<!-- more -->

If you are writing a module or a library that will be required by others, do not change the behavior of other third-party modules that will affect the entire application.

This is similar to another rule: Do not check the environment settings in a module or a library. But if you have to do it, run LoDash in context, so the behavior change will be scoped to the module or the script:

> Create a pristine lodash function to avoid conflicts caused by modifying the original. - https://lodash.com/docs#runInContext

Here is an example:

```js
// Run LoDash in Context
// =====================
//
// Run LoDash in context in order to change the behavior of template settings.
//
// Use two different template interpolation styles.

'use strict';


// Dependencies
// ------------
//
// Run LoDash as usual.
var _    = require('lodash');
var tmpl = 'Hello <%= name %>!';


// Main
// ----
(function () {
  // Run LoDash in a context.
  var _    = require('lodash').runInContext();
  var tmpl = 'Hello {{ name }}!';

  // Use mustache style instead of the default.
  //
  // Notice: Due to static page generating error, backslashes before each of
  // four braces can be removed in the actual JavaScript file.
  _.templateSettings.interpolate = /\{\{([\s\S]+?)\}\}/g;
  console.log(_.template(tmpl)({ name: 'Foo' }));
})();

// Use the default template style.
console.log(_.template(tmpl)({ name: 'Foo' }));
```

Execute it:

```sh
$ node run-in-context.js
Hello Foo!
Hello Foo!
```

[LoDash]: https://lodash.com/
[module caching]: https://nodejs.org/api/modules.html#modules_caching
