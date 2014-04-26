title: Repeat Character Serveral Times in JavaScript
date: 2013-06-09 09:55:23
tags: [javascript, array]
---

In [Markdown][], a header can be written as:

```markdown
Header
======
```

This is a Setext-style headers, which are _'underlined' using equal signs (for
first-level headers) and dashes (for second-level headers)_. In order to
generate those underlined equal signs, a simple loop could be used to repeat
the character `=` several times. By the way, it is not necessary to have exact
number of the equal signs as the header length, but it just looks better. But
there is [another way][so]:

```JavaScript
Array(heading.length + 1).join('=')
```

It is using the JavaScript [Array][] global object to construct an empty Array
with the specified array length. Clever indeed!

[markdown]: http://daringfireball.net/projects/markdown/
[so]: http://stackoverflow.com/a/1877479/594696
[array]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array
