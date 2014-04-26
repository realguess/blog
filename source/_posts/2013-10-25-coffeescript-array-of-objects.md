title: CoffeeScript Array of Objects
date: 2013-10-25 14:48:10
tags: [coffeescript, array, object, coding-style]
---

We frequently find ourselves need to define array of objects. In CoffeeScript we can write in the following way:

```coffeescript
arr = [
  {
    name: 'foo'
    pass: 'oof'
  }
  {
    name: 'bar'
    pass: 'rab'
  }
]
```

Or another format is to insert a comma:

```coffeescript
arr = [
  name: 'foo'
  pass: 'oof'
,
  name: 'bar'
  pass: 'rab'
]
```

In the spirit of CoffeeScript, we want to avoid using braces as much as possible. Therefore, the second method is preferred.
