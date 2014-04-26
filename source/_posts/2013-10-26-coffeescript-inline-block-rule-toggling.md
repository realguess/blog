title: CoffeeScript Inline Block Rule Toggling
date: 2013-10-26 23:06:00
tags: [coffeescript, coffeelint]
---

Temporarily remove [CoffeeLint][] restriction via the inline code:

```coffeescript
# coffeelint: disable=max_line_length,no_implicit_parens
#
# A long comment that exceeds 80 chars per line. Lorem ipsum dolor sit amet, consectetur
# adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
#
# coffeelint: enable=max_line_length,no_implicit_parens
```


[coffeelint]: http://www.coffeelint.org/
