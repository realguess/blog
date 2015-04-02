title: Test Execution Order in Mocha
date: 2015-04-01 12:00:00
categories: [Node.js and io.js]
tags: [order, synchronous, asynchronous, mocha, test]
---

In synchronous tests, both `describe` and `it` statements are executed in the order they are laid out.

Test `Foo` should run before `Bar`:

```js
describe('Suite', function () {
  it('Foo', function () {
  });

  it('Bar', function () {
  });
});
```

Suite `Foo` should run before `Bar`:

```js
describe('Foo', function () {
});

describe('Bar', function () {
});
```

In asynchronous tests, the same ordering applies.

Test Foo should run before Bar even Foo takes much longer to execute, and Suite B should also run after Suite A:

```js
var should = require('should');

describe('Suite A', function () {
  it('Foo', function (done) {
    setTimeout(function () {
      (true).should.equal(true);
      done();
    }, 1000);
  });

  it('Bar', function () {
    (true).should.equal(true);
  });
});

describe('Suite B', function () {
  it('Foo', function () {
    (true).should.equal(true);
  });
});
```

Result:

```
  Suite A
    ✓ Foo (1002ms)
    ✓ Bar 

  Suite B
    ✓ Foo 


  3 passing (1s)
```

This is the great feature in [Mocha].

[Mocha]: http://mochajs.org/
