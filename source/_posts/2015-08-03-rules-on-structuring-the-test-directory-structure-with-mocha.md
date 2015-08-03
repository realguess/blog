title: Rules on Structuring the Test Directory Structure with Mocha
date: 2015-08-03 12:00:00
categories: [Test]
tags: [mocha, structure, unix-philosophy]
---

When writing tests, the framework I use for [Node.js] is [Mocha] (reviewed version: v2.2.x). But how should I structure the `./test` directory? I first started by grouping related tests into directory, for example:

```sh
$ tree ./test
./test
├── posts
│   └── main.js
└── users
    └── main.js
```

You can use `mocha --recursive` to run all test suites.

However, it's unlikely that all files in the test directory are test suites. You might need to define some shared utilities, data generation scripts, or even setup files. You don't want these files to be executed by Mocha. You can get away by using:

 - `mocha --grep` or `mocha --fgrep`
 - Use `find` and `xargs` to locate the files to be executed by Mocha

But these are getting more and more complex. You want something simple and dumb frankly. Therefore, a better approach is to follow the two guidelines. One is the default behavior of Mocha:

<!-- more -->

> By default, Mocha looks for the glob `./test/*.js`. - http://mochajs.org/#the-test-directory

Another one is a rule in the Unix philosophy:

> **Rule of Representation**: Developers should choose to make data more complicated rather than the procedural logic of the program when faced with the choice, because it is easier for humans to understand complex data compared with complex logic. - https://en.wikipedia.org/wiki/Unix_philosophy

By following the two guidelines, we should structure the test suites off `./test` directory on the top-level only, such as:

```sh
$ tree ./test
./test
├── lib
│   └── config.js
└── main.js
```

In this test directory structure, the two JavaScript files defined are:

 - `main.js`: A test suite file, the one with `describe` statement.
 - `lib/config.js`: A library file for supporting the test suite.

No more figuring out the pattern to run tests, just `mocha`. Simple is better. And we have pushed the complexity to data (test directory structure) instead of code (glob or patterns).

In conclusion, the rules I have come up with for structuring the test directory with Mocha:

1. All test suites should be placed on the first level off the `test/` directory, such as `test/main.js`. All test suite files should include the `describe` statement.
2. All other library files or data generation files should be placed on its own directory, and they should not include the `describe` statement.

[Node.js]: https://nodejs.org/
[Mocha]: http://mochajs.org/
