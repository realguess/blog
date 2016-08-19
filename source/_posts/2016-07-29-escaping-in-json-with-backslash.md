title: Escaping in JSON with Backslash
date: 2016-07-29 12:00:00
categories: [Coding]
tags: [backslash, escape-character, json, javascript]
---

> Escape characters are part of the syntax for many programming languages, *data formats*, and communication protocols. For a given alphabet an escape character's purpose is to start character sequences (so named escape sequences), which have to be interpreted differently from the same characters occurring without the prefixed escape character.[^2]

JSON or JavaScript Object Notation is a *data interchange format*. It has an escape character as well.

> In many programming languages such as C, Perl, and PHP and in Unix scripting languages, the backslash is an escape character, used to indicate that the character following it should be treated specially (if it would otherwise be treated normally), or normally (if it would otherwise be treated specially).[^3]

JavaScript also uses backslash as an escape character. JSON is based on a subset of the JavaScript Programming Language, therefore, JSON also uses backslash as the escape character:

> A string is a sequence of zero or more Unicode characters, wrapped in double quotes, using backslash escapes.[^1]

A character can be:

- Any Unicode character except `"` or `\` or control character
- `\"`
- `\\`
- `\/`
- `\b`
- `\f`
- `\n`
- `\r`
- `\t`
- `\u` + four-hex-digits

Only a few characters can be escaped in JSON. If the character is not one of the listed:

```
$ cat data.json
"\a"
```

it returns a `SyntaxError`[^4]:

```
$ node -e 'console.log(require("./data.json"))'
module.js:561
    throw err;
    ^

SyntaxError: /home/chao/tmp/js/data.json: Unexpected token a in JSON at position 2
    at Object.parse (native)
    at Object.Module._extensions..json (module.js:558:27)
    at Module.load (module.js:458:32)
    at tryModuleLoad (module.js:417:12)
    at Function.Module._load (module.js:409:3)
    at Module.require (module.js:468:17)
    at require (internal/module.js:20:19)
    at [eval]:1:13
    at ContextifyScript.Script.runInThisContext (vm.js:25:33)
    at Object.exports.runInThisContext (vm.js:77:17)
```

<!-- more -->

Why do we care about the escaping backslash in JSON? Because when you're writing a JSON string, if there's a backslash in the string, you have to escape it by prefixing with another backslash. For example, the following `sed` command emphasizes every word:

```
$ echo foo and bar | sed 's/\(\w\+\)/*\1*/g'
*foo* *and* *bar*
```

Encode into a JSON string, every backslash needs to be escaped by backslash:

```
"s/\\(\\w\\+\\)/*\\1*/g"
```

Write in JavaScript with JSON string, you need to *escape the escape*, resulting quadruple backslashes:

```
$ node -e 'console.log(JSON.parse("\"s/\\\\(\\\\w\\\\+\\\\)/*\\\\1*/g\""))'
s/\(\w\+\)/*\1*/g
```

Therefore, it's better to separate data from logic, and write JSON literal in its own file.


[^1]: [Introducing JSON](http://json.org/)
[^2]: [Escape character](https://en.wikipedia.org/wiki/Escape_character), Wikipedia
[^3]: [Backslash](https://en.wikipedia.org/wiki/Backslash), Wikipedia
[^4]: The tested Node.js version is `v6.3.1`.
