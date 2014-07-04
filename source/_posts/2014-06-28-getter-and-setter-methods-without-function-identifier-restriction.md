title: Getter and Setter Methods without Function Identifier Restriction
date: 2014-06-28 10:10:21
tags: [getter, setter, function, identifier, javascript]
---

A JavaScript identifier must start with either a letter, underscore, or dollar sign:

```js
> function 7/11(){}
SyntaxError: Unexpected number
> function '7/11'(){}
SyntaxError: Unexpected string
> var 7/11 = function(){}
SyntaxError: Unexpected number
> var '7/11' = function(){}
SyntaxError: Unexpected string
```

But object property does have such a limitation:

```js
> var obj = { '7/11': function(){} }
> obj['7/11']
function (){}
```

And it works with accessor properties or getter and setter methods:

```js
> var obj = { get '7/11'(){ return '7/11'; } }
> obj['7/11']
"7/11"
```

The syntax `get '7/11'(){}` looks like `function '7/11'(){}`, but it is not, getter and setter are still object properties. That is why it works. That's another reason to use JavaScript object.
