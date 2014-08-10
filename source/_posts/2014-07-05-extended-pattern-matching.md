layout: blog
title: Extended Pattern Matching
date: 2014-07-05 14:51:30
tags: [extended-pattern-matching, extglob, pattern-matching, glob, brace-expansion, shopt, bash, minimatch]
---

Bash supports [extended pattern matching]. By using the built-in utility we can check if it is enabled or not:

    $ shopt extglob

If not, to enable it:

    $ shopt -s extglob

By default `extglob` is on in interactive shells, but **off in non-interactive shells**.

The key about extended pattern matching is pattern list via `|` (what we usually see as a `OR` operator). But do not think about it as that, think about it as a list of array pattern that are separated by `|` instead of `,`. And actually one of them is the same as `{}` or brace expansion, but it can do more than expanding.

Here are the pattern operators:

    ? * + @ !

Create some example files:

    $ touch a{,1,2,11,12}.js && ls
    a11.js  a12.js  a1.js  a2.js  a.js

**`?(pattern-list)`**

Zero or one (any one) occurrence of the giving pattern:

    $ ls a?(2|1).js
    a1.js  a2.js  a.js

**`*(pattern-list)`**

Zero or more occurrences of the giving pattern (essentially everything):

    $ ls a*(2|1).js
    a11.js  a12.js  a1.js  a2.js  a.js

**`+(pattern-list)`**

One or more of the giving pattern (notice `a.js` is missing):

    $ ls a+(2|1).js
    a11.js  a12.js  a1.js  a2.js

**`@(pattern-list)`**

Any one of giving pattern:

    $ ls a@(2|1).js
    a1.js  a2.js

**`!(pattern-list)`**

None of the giving pattern:

    $ ls a!(2|1).js
    a11.js  a12.js  a.js

One mistake I had was getting confused between extended pattern matching and brace expansion, for example:

    $ ls test/@{src|spec}/*.js
    ls: cannot access test/@src/*.js: No such file or directory
    ls: cannot access test/@spec/*.js: No such file or directory

Extended pattern uses parentheses `()` not braces `{}` as in brace expansion.

Also, these two patterns are the same:

    test/@(src|spec)/*.js
    test/{src,spec}/*.js

In some situations, extended pattern matching does not work, for example, matching files from the current directory and from one of the subdirectories with the following directory structure:

```
$ tree
.
├── app.js
├── lib
│   └── util.js
└── test
    └── main.js

2 directories, 3 files
```

I would like to match js files from the current directory and `lib/` directory, sort of like:

    $ ls *.js lib/*.js
    app.js  lib/util.js

But this does not do it:

    $ ls @(.|lib)/*.js
    
Instead, use brace expansion:

    $ ls {.,lib}/*.js

Finally, Node's [Minimatch] supports brace expansion, _extended globbing_ and globstar.

[extended pattern matching]: https://www.gnu.org/software/bash/manual/html_node/Pattern-Matching.html
[Minimatch]: https://github.com/isaacs/minimatch
