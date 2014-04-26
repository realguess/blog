layout: blog
title: Immediately Invoked Function Expression in CoffeeScript
date: 2014-03-03 13:39:13
tags: [immediately-invoked-function-expression, iife, function-expression, function, coffeescript]
---

[CoffeeScript] only has function expression, there are no named functions nor function declaration, as [almost everything in CoffeeScript is expression][expression].

Anonymous function can be written as just a dash plus an arrow:
```coffeescript
->
```

This will be compiled to JavaScript:
```javascript
(function () {});
```

This anonymous function can be invoked immediately:
```javascript
// (->)()
(function () {})();
```

The immediately invoked function expression (IIFE) syntax is different from the [preferred format][iife] in JavaScript. But this is more likely due to the syntax and compilation of CoffeeScript.

Here is one useful example to show the syntax:
```javascript
// (square = (n) -> return n * n)()
var square;

(square = function(n) {
  return n * n;
})();
```

And a few more without compiled Javascript:
```coffeescript
(->)()
(square = (n) -> return n * n)()
console.log (square = (n) -> return n * n)(10) # 100
```

[coffeescript]: http://coffeescript.org/
[expression]: http://coffeescript.org/#expressions
[iife]: /2014/03/02/immediately-invoked-function-expression/
