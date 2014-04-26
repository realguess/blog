title: JavaScript parseInt(array) Only Converts the First Element
date: 2013-10-02 22:23:58
tags: [javascript, parseint]
---

Converting from one type to another frequently causes bugs or unexpected result if you're not careful. For example, when converting a string to an integer, we use the following function:

```javascript
var num = parseInt(string, radix);
```

Accidentally, I used an array of numeric strings in place of string for conversion.

As stated in [MDN parseInt][parseInt]:

> If string is not a string, then it is converted to one. Leading whitespace in the string is ignored.

The array is converted to string.

If the array has only one element, then there won't be any problem:

```javascript
[10].toString()
> '10'
parseInt('10', 10)
> 10
```

But when there are more than one element, only the first element will be parsed:

```javascript
[10, 5].toString()
> '10,5'
parseInt('10,5', 10)
> 10
```

Therefore, you have to be very careful when processing query parameter like:

```
?year=2013&year=2012
```

In many cases, it will be converted to:

```javascript
year: ['2013', '2012']
```

After running through `parseInt`, it will result in `2013`, might not be what you are looking for.

[parseInt]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt
