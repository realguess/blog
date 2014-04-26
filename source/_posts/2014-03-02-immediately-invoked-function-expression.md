title: Immediately Invoked Function Expression
date: 2014-03-02 09:55:23
tags: [function-innovation, immediately-invoked-function-expression, iife, function-declaration, function-expression, function, javascript]
---

What's difference between:
```javascript
(function(){}());
(function(){})();
```

This is function declaration:
```javascript
function foo(){}
```

The identifier of the function declaration is required.

This is function expression:
```javascript
var foo = function(){};
```

The right hand side of the assignment must be an expression. And function identifier is optional. Check the previous article about the difference between [function declaration and function expression][func].

Now, come in the grouping operator, which return the result of evaluating an expression:
```javascript
(function(){});
```

If we do it with named function:
```javascript
function foo(){}
(foo);
```

or
```javascript
(function foo(){});
```

But we haven't done anything here, in another word, we haven't invoked the function. We merely evaluate the function in three occasions. Function is object in JavaScript, when you're evaluating an object, the result is still an object:
```javascript
console.log(function foo(){}); // [Function: foo]
console.log(function (){});    // [Function]
function bar(){}
console.log(bar);              // [Function: bar]
console.log({});               // {}
```

Now, let's apply function invocation, and do it the traditional way:
```javascript
foo();
```

The function has been invoked, and it will return the result of the function.

Replace the identifier with function declaration:
```javascript
function foo(){}();
```

However, this does not work. Because interpreter treats it as a function declaration, and `(` will become an unexpected token. Function declaration cannot be invoked immediately. So, we need to make it an expression, by using the grouping operator:
```javascript
(function foo(){})();
```

The function identifier is no longer necessary:
```javascript
(function(){})();
```

Since grouping operator return the result of the expression, we can just drop it:
```javascript
function(){}();
```

But this statement becomes a function declaration again, so we need to place the grouping operator around it:
```javascript
(function(){}());
```

Okay, but what's difference between:
```javascript
(function(){}());
(function(){})();
```

There is no difference, they are both function expressions being invoked immediately.  Function invocation can only be done via function expression. But first one is more align with `foo()`, the traditional way. According to the [Code Convention for JavaScript Programming Language][code] by Douglas Crockford:

> When a function is to be invoked immediately, the entire _invocation expression_ should be wrapped in parens so that it is clear that the value being produced is the result of the function and not the function itself.

The invocation expression is `function(){}()`, and wrap it around parenthesis becomes `(function(){}());`.

Therefore, this first method is more traditional and preferred.

[func]: /2014/03/01/function-declaration-and-function-expression/
[code]: http://javascript.crockford.com/code.html
