title: Command Line JSON Processing
date: 2014-05-18 14:49:04
tags: [jq, json, command-line]
---

What is the best command line tool to process JSON?

Hmm... Okay, let's try different command line JSON processing tools with the following use case to decide which one is the best to use.

Here is the use case: `JSON | filter | shell`. A program outputs JSON data, pipes into a JSON command line processing tool to filter data, and then send to a shell command to do more work.

Here is a snippet of sample JSON data:

```json
[
  {
    "id": 1,
    "username": "foo",
    "name": "Foo Foo"
  },
  {
    "id": 2,
    "username": "bar",
    "name": "Bar Bar"
  }
]
```

Or in one-liner `data.json`:

```json
[{"id":1,"username":"foo","name":"Foo Foo"},{"id":2,"username":"bar","name":"Bar Bar"}]
```

The command line JSON processor should filter each element of the array and convert it into its own line:

```
{"name":"Foo Foo"}
{"name":"Bar Bar"}
```

The result will be piped as the input line by line into a shell script `echo.bash`:

```bash
#!/usr/bin/env bash

while read line; do
  echo "ECHO: '"$line"'"
done
```

The final output should be:

```
ECHO: '{"name":"Foo Foo"}'
ECHO: '{"name":"Bar Bar"}'
```


Custom Solution
---------------

Before start looking for existing tools, let's see how difficult it is to write a custom solution.

```javascript
// Filter and convert array element into its own line.
var rl = require('readline').createInterface({
  input : process.stdin,
  output: process.stdout,
});

rl.on('line', function (line) {
  JSON.parse(line).forEach(function (item) {
    console.log('{"name":"' + item.name + '"}');
  });
}).on('close', function () {
  // Shh...
});
```

Perform a test run:

```
$ cat data.json | node filter.js  | bash echo.bash 
ECHO: '{"name":"Foo Foo"}'
ECHO: '{"name":"Bar Bar"}'
```

Well, it works. In essence, we are writing a simple JSON parser. Unless you want to keep the footprint small, you don't want to write another JSON parser. And why bother to reinvent the wheel? Let's start look at the existing solutions.


Node Modules
------------

Let's start with the tools from NPM registry:

    $ npm search json command

Here are a few candidates that appears to be matching from the description:

- [jku](https://github.com/bjorne/jku) - Jku is a command-line tool to filter and/or modifiy a JSON stream. It is heavily inspired by jq. (2 stars and not active, last update 8 months ago).
- [json](https://github.com/zpoley/json-command) or [json-command](https://github.com/zpoley/json-command) - JSON command line procesing toolkit. (122 stars and 14 forks, last update 9 months ago)
- [jutil](https://github.com/misterfifths/jutil) - Command-line utilities for manipulating JSON. (88 stars and 2 forks, last update more than 2 years ago)

Not a lot of choice, and modules are not active. This might be that because there is already a really good solution, [jq], which has 2493 stars and 145 forks, and the last update was 6 days ago.


jq
--

> jq is like `sed` for JSON data - you can use it to slice and filter and map and transform structured data with the same ease that `sed`, `awk`, `grep` and friends let you play with text. - [jq]

Instead of NPM install, do:

    $ sudo apt-get -y install jq

Since we don't need color or prettified, just line by line. So, here is the command chain:

```
$ cat data.json | jq -c -M '.[] | {name}'  | bash echo.bash
ECHO: '{"name":"Foo Foo"}'
ECHO: '{"name":"Bar Bar"}'
```

[jq] can do much more than just the example just shown. It has zero runtime dependencies, and flexible to deal with not just array but object as well.


Conclusion
----------

[jq] is clearly the winner here, with the least dependency, the most functionality and more popularity, as well as a comprehensive documentation.


[jq]: https://github.com/stedolan/jq

