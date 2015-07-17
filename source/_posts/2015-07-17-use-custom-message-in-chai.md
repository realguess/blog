title: Use Custom Message in Chai
date: 2015-07-17 12:00:00
categories: [Test]
tags: [chai, mocha]
---

When running a test in a loop with [Mocha] and [Chai], it might not be easy to pinpoint the element where the error occurred. For example:

```js
for (let i = 0, len = arr.length; i < len; i += 1) {
  expect(arr[i]).to.equal(true);
}
```

Run the test:

```sh
$ mocha --harmony test/message.js


  Custom Message
    1) Should return true


  0 passing (37ms)
  1 failing

  1) Custom Message Should return true:

      AssertionError: expected false to equal true
      + expected - actual

      -false
      +true

      at Context.<anonymous> (test/message.js:16:25)
```

It failed on the `expect` statement, but the error message does not show which element is in error.

Instead, we can add a custom message to the `expect` statement:

```js
for (let i = 0, len = arr.length; i < len; i += 1) {
  expect(arr[i], 'i = ' + i).to.equal(true);
}
```

<!-- more -->

Now, when running the test, you can pinpoint the source of the error:

```sh
$ mocha --harmony test/message.js


  Custom Message
    1) Should return true


  0 passing (17ms)
  1 failing

  1) Custom Message Should return true:

      i = 1
      + expected - actual
      -false
      +true

      at Context.<anonymous> (test/message.js:16:37)
```

The error occurs at the second element.

Reference:

```js
// Custom Message in Chai
// ======================

'use strict';

describe('Custom Message', function () {

  // Dependencies
  // ------------
  var expect = require('chai').expect;
  var arr    = [ true, false, true ];

  it('Should return true', function () {
    for (let i = 0, len = arr.length; i < len; i += 1) {
      expect(arr[i], 'i = ' + i).to.equal(true);
    }
  });

});
```

[Mocha]: http://mochajs.org/
[Chai]: http://chaijs.com/