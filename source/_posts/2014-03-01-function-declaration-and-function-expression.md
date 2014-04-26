title: Function Declaration and Function Expression
date: 2014-03-01 00:04:36
tags: [function-declaration, function-expression, named-function, anonymous-function, function, javascript]
---

In JavaScript, what is _function declaration_?

    function square(n) { return n * n; }

Here we declare a function and name it "square". But what is _function expression_?

    var square = function (n) { return n * n; };

When you start a statement with keyword `function`, then the statement is _function declaration_, everything else is _function expression_.

Function expression can either be named or anonymous. The above is anonymous function, and here is a named one:

    var square = function square(n) { return n * n; };

There is a variable `square` and function name `square`, but they are not the same. The scopes are different. The function name `square` of the function expression can be only used inside the function, mainly for debugging purpose. Accessing from outside the function, will throw `ReferenceError`:

    var foo = function bar() {};
    bar(); // ReferenceError: bar is not defined

We can also use property 'name' to differentiate a named vs. an anonymous function:

    console.log(square.name); // empty string for an anonymous function

However, you cannot create an anonymous function declaration, for a very obvious reason:

    function (n) { return n * n; } // SyntaxError: Unexpected token (

Another difference between function declaration and function expression is hoisting:

    square(10); // 100
    function square(n) { return n * n; }

Function defined by declaration is hoisted, but not for function expression:

    square(10); // TypeError: undefined is not a function
    var square = function (n) { return n * n; }

The variable declaration is hoisted, but not for the definition. Therefore, result in `TypeError`. However, for best practice, functions should be defined before being used.

In conclusion:

- Function declaration must start with keyword `function` in the beginning of the statement. Everything else is function expression.
- Function expression can be anonymous and named, but function declaration can only be named.
- Function declaration is hoisted, but not function expression.

This function declaration happens inside a unreachable block.
    
    square(10); // 100
    if (false) {
      function square(n) { return n * n; }
    }

There is no block scope in JavaScript. Spaces in the beginning of keyword `function` do not count. This is still a valid function declaration.

A simple way to turn function declaration into function expression is by wrapping around parentheses:

    (function square(n) { return n * n; });

We are applying the grouping operator `()` here, which evaluates the function expression.

Another way:

    foo(err, function(){});

Just think about `,` as `()`.

Other good reads:

- StackOverflow, [var functionName = function() {} vs function functionName() {}](http://stackoverflow.com/q/336859/594696)
- Angus Croll, [Function Declarations vs. Function Expressions](http://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/)
- MDN, [Function constructor vs. function declaration vs. function expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope#Function_constructor_vs._function_declaration_vs._function_expression)
