title: Avoid Assigning undefined to an Object Property
date: 2014-06-21 11:58:22
tags: [object, json, stringify, serialization, javascript]
---

A property value should be any JavaScript value except for `undefined`. If you do something like this (albeit it is legal):

```js
var foo = { bar: undefined };
```

will leads to confusing code. Because when accessing the property value:

```js
console.log(foo.bar); // undefined
```

It will returns `undefined`. But you are not sure if it means the property exists or not or the value of the property is set to `undefined`. Therefore, you should do:

```js
var foo = { bar: null };
```

This indicates that the property is expected, and with the value of `null`.

You do be able to check the existence of a property by:

```js
Object.keys(foo); // ['bar']
foo.hasOwnProperty('bar'); // true
```

But more importantly, if you serialize the object with `JSON.stringify`, properties with `undefined` will be omitted:

```js
JSON.stringify({ bar: undefined }); // '{}'
```

According to [JSON specification][json], _a value can be a `string` in double quotes, or a `number`, or `true` or `false` or `null`, or an `object` or an `array`_. `undefined` is not a valid JSON value.

`null` is fine:

```js
JSON.stringify({ bar: null }); // '{"bar":null}'
```

So, for the best practice, avoid assigning `undefined` to a property of an object. Use `null` to indicate the expected property without a value. This will increase portability when using JSON to serialize.

[json]: http://www.json.org/

