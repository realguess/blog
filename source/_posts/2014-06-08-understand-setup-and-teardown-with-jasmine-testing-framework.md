title: Understand Setup and Teardown with Jasmine Testing Framework
date: 2014-06-08 09:36:42
tags: [jasmine, testing, setup, beforeeach, teardown, aftereach]
---

In [Jasmine], the testing structure of setup, test, and teardown is done by using `beforeEach/it+/afterEach`. Global `beforeEach` and `afterEach` functions are called once before each spec in the `describe`.

Let's walk through a few examples.

Before describing any example, set up a function to watch how the value changes from executing one spec to another. [Object.observe] is yet to be available in Firefox, but we can use something similar with [Object.watch]:

```javascript
// Specific to Firefox only.
function watchHandler(prop, oldVal, newVal) {
  console.log(prop + ' changed from ' + oldVal + ' to ' + newVal);
  return newVal;
}
```

The first example is to perform the setup without using `beforeEach`:

```javascript
describe('Without "beforeEach"', function () {
  var obj   = {};
  var prop  = 'foo1';
  obj[prop] = 0;
  obj.watch(prop, watchHandler); // Firefox only

  // Setup
  obj[prop] += 1;

  it(prop + ' should be equal to 1', function () {
    expect(obj[prop]).toBe(1);
  });

  it(prop + ' should not be equal to 2', function () {
    expect(obj[prop]).not.toBe(2);
  });
});
```

The `obj` property `foo1` was changed once:

```
LOG: 'foo1 changed from 0 to 1'
```

If using `beforeEach` to perform setup before each spec:

```javascript
describe('With "beforeEach"', function () {
  var obj   = {};
  var prop  = 'foo2';
  obj[prop] = 0;
  obj.watch(prop, watchHandler); // Firefox only

  // Setup
  beforeEach(function () {
    obj[prop] += 1; // Should be invoked twice.
  });

  it(prop + ' should be equal to 1', function () {
    expect(obj[prop]).toBe(1);
  });

  it(prop + ' should be equal to 2', function () {
    expect(obj[prop]).toBe(2);
  });
});
```

Then we should expect the value to be updated twice. There are two specs, and for each spec, the `beforeEach` will be invoked:

```
LOG: 'foo2 changed from 0 to 1'
LOG: 'foo2 changed from 1 to 2'

```

If we change the values inside a spec without using `beforeEach`:

```javascript
describe('Without "beforeEach" but updating in spec', function () {
  var obj   = {};
  var prop  = 'foo3';
  obj[prop] = 0;
  obj.watch(prop, watchHandler); // Firefox only

  // Setup
  obj[prop] += 1;

  it(prop + ' should be equal to 1', function () {
    expect(obj[prop]).toBe(1);
    obj[prop] += 1;
  });

  it(prop + ' should be equal to 2', function () {
    expect(obj[prop]).toBe(2);
  });
});
```

The value has been incremented to 2 inside the first spec before the execution of the second spec:

```
LOG: 'foo3 changed from 0 to 1'
LOG: 'foo3 changed from 1 to 2'
```

With using `beforeEach`:

```javascript
describe('With "beforeEach" and updating in spec', function () {
  var obj   = {};
  var prop  = 'foo4';
  obj[prop] = 0;
  obj.watch(prop, watchHandler); // Firefox only

  // Setup
  beforeEach(function () {
    obj[prop] += 1;
  });

  it(prop + ' should be equal to 1', function () {
    expect(obj[prop]).toBe(1);
    obj[prop] += 1;
  });

  it(prop + ' should be equal to 3', function () {
    expect(obj[prop]).toBe(3);
  });
});
```

Now, before each spec, the value was incremented, and inside the first spec, the value was also incremented:

```
LOG: 'foo4 changed from 0 to 1'
LOG: 'foo4 changed from 1 to 2'
LOG: 'foo4 changed from 2 to 3'
```

To ensure the value stays the same before each spec, we can use `afterEach`:

```javascript
describe('Reset with "afterEach"', function () {
  var obj   = {};
  var prop  = 'foo5';
  obj[prop] = 0;
  obj.watch(prop, watchHandler); // Firefox only

  // Setup
  beforeEach(function () {
    obj[prop] += 1;
  });

  it(prop + ' should be equal to 1', function () {
    expect(obj[prop]).toBe(1);
    obj[prop] += 1;
  });

  it(prop + ' should still be equal to 1', function () {
    expect(obj[prop]).toBe(1);
  });

  // Teardown
  afterEach(function () {
    obj[prop] = 0;
  });
});
```

After each spec, the value has been reset, and before the start of next spec, the value has been incremented from original value `0` to `1` again:

```
LOG: 'foo5 changed from 0 to 1'
LOG: 'foo5 changed from 1 to 2'
LOG: 'foo5 changed from 2 to 0'
LOG: 'foo5 changed from 0 to 1'
LOG: 'foo5 changed from 1 to 0'
```

The locations of `beforeEach` and `afterEach` do not matter:

```javascript
describe('Reverse the order of "beforeEach" and "afterEach"', function () {
  var obj   = {};
  var prop  = 'foo6';
  obj[prop] = 0;
  obj.watch(prop, watchHandler); // Firefox only

  // Teardown
  afterEach(function () {
    obj[prop] = 0;
  });

  it(prop + ' should be equal to 1', function () {
    expect(obj[prop]).toBe(1);
    obj[prop] += 1;
  });

  it(prop + ' should still be equal to 1', function () {
    expect(obj[prop]).toBe(1);
  });

  // Setup
  beforeEach(function () {
    obj[prop] += 1;
  });
});
```

Here we switch the setup and teardown, but the result is the same:

```
LOG: 'foo6 changed from 0 to 1'
LOG: 'foo6 changed from 1 to 2'
LOG: 'foo6 changed from 2 to 0'
LOG: 'foo6 changed from 0 to 1'
LOG: 'foo6 changed from 1 to 0'
```

Because the content of the suite is read before any of specs is executed.

Asynchronous setup, test, and teardown:

```javascript
describe('Async with "beforeEach" and "afterEach"', function () {
  var obj   = {};
  var prop  = 'foo7';
  obj[prop] = 0;
  obj.watch(prop, watchHandler); // Firefox only

  // Setup
  beforeEach(function (done) {
    setTimeout(function () {
      obj[prop] += 1;
      done();
    }, 100);
  });

  it(prop + ' should be equal to 1', function (done) {
    setTimeout(function () {
      expect(obj[prop]).toBe(1);
      obj[prop] += 1;
      done();
    }, 100);
  });

  it(prop + ' should still be equal to 1', function (done) {
    setTimeout(function () {
      expect(obj[prop]).toBe(1);
      done();
    }, 100);
  });

  // Teardown
  afterEach(function () {
    setTimeout(function () {
      obj[prop] = 0;
    }, 100);
  });
});
```

Work the same:

```
LOG: 'foo7 changed from 0 to 1'
LOG: 'foo7 changed from 1 to 2'
LOG: 'foo7 changed from 2 to 0'
LOG: 'foo7 changed from 0 to 1'
LOG: 'foo7 changed from 1 to 0'
```

Here is the [gist] for all examples shown above.

[Jasmine]: http://jasmine.github.io/
[Object.observe]: http://wiki.ecmascript.org/doku.php?id=harmony:observe
[Object.watch]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/watch
[gist]: https://gist.github.com/realguess/71b89263ebb11521cf49

