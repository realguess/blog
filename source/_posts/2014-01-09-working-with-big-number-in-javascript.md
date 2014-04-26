title: Working with Big Number in JavaScript
date: 2014-01-09 09:18:19
tags: [big-number, twitter, id, javascript, node]
---

JavaScript is only capable of handling 53-bit numbers, if you are working with a big number such as [Twitter ID][], which is using 64-bit number, then you need to find an external library to do that, otherwise, there will be precision lost:

    > num = 420938523475451904
    420938523475451900
    > num = 420938523475451904 + 1
    420938523475451900
    > num = 420938523475451904 - 1
    420938523475451900

Here is one library to use in Node environment, install [Big.js][]:

    $ npm install big.js

Load the module:

    > BigNum = require('big.js')
    { [Function: Big] DP: 20, RM: 1 }

Use string to create the big number:

    > num = BigNum('420938523475451904')
    { s: 1,
      e: 17,
      c: [ 4, 2, 0, 9, 3, 8, 5, 2, 3, 4, 7, 5, 4, 5, 1, 9, 0, 4 ] }
    > num.toString()
    '420938523475451904'

Perform addition:

    > num.plus(1).toString()
    '420938523475451905'

Perform substraction:

    > num.minus(1).toString()
    '420938523475451903'

There are other packages that yet to be tested:

- [bignumber.js](https://github.com/MikeMcl/bignumber.js/)
- [strint.js](https://github.com/rauschma/strint)
- [bignumber](http://jsfromhell.com/classes/bignumber)
- [JavaScript bignumber](https://github.com/jtobey/javascript-bignum)


[twitter id]: https://dev.twitter.com/docs/twitter-ids-json-and-snowflake
[big.js]: https://github.com/MikeMcl/big.js/
