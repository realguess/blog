title: Inherited Accessor Property Can Only Be Overridden by Accessor Property
date: 2014-06-29 21:07:07
tags: [accessor-property, inheritance, object-attributes, getter, setter, object, javascript]
---

In Javascript, define a property `prop` as an accessor property via getter/setter:

```js
var obj = {
  get prop() {},
  set prop() {}
};
```

And the property will have the following attributes:

    > Object.keys(obj)
    [ 'prop' ]
    > Object.getOwnPropertyDescriptor(obj, 'prop')
    { get: [Function: prop],
      set: [Function: prop],
      enumerable: true,
      configurable: true }

Now, create a new object that inherits `obj`, and attempt to overwrite the accessor property by a data property:

```js
var foo = Object.create(obj);
foo.prop = 'data';
```

But, the same property of the new object cannot be created:

    > Object.keys(foo)
    []
    > Object.getOwnPropertyDescriptor(foo, 'prop')
    undefined

That is because that the property is an accessor property and it cannot be overridden by a data property. It can only be overridden by an accessor property:

```js
Object.defineProperty(foo, 'prop', {
  get: function prop() {},
  set: function prop() {},
  enumerable: true,
  configurable: true,
});
```

Then, the `foo` object will have its own property named `prop`:

    > Object.keys(foo)
    [ 'prop' ]
    > Object.getOwnPropertyDescriptor(foo, 'prop')
    { get: [Function: prop],
      set: [Function: prop],
      enumerable: true,
      configurable: true }
