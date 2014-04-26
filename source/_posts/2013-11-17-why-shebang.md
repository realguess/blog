title: Why Shebang?
date: 2013-11-17 12:30:17
tags: [shebang, shabang, shell, script, interpreter, linux, unix, node]
---

Frequently the initial two characters on the initial line of a script are: `#!`.

_Why [Shebang][]?_ Well, simple reason, because shell needs to know which interpreter to use when executing the script.

> The sha-bang (`#!`) at the head of a script tells your system that this file is a set of commands to be fed to the command interpreter indicated. The `#!` is actually a two-byte magic number, a special marker that designates a file type, or in this case an executable shell script (type man magic for more details on this fascinating topic). Immediately following the sha-bang is a path name. This is the path to the program that interprets the commands in the script, whether it be a shell, a programming language, or a utility. This command interpreter then executes the commands in the script, starting at the top (the line following the sha-bang line), and ignoring comments. - [Starting Off With a Sha-Bang][abs]

The shebang line is usually ignored by the interpreter because the `#` character is a comment marker in many scripting languages. Even the actual script is written with a different commenting character, such as in JavaScript:

```shell
#!/usr/bin/env node
// JavaScript stuff starts here.
```

Because the first line is interpreted by the shell, and the rest is passed into JavaScript interpreter, in this case, Node.

The syntax of shebang:

    #! interpreter [optional-arg]

Whether there is a space between shebang character and the interpreter or not, it does not matter.

The interpreter must usually be an absolute path to a program that is not itself a script. The following are example interpreters:

```shell
#! /bin/sh
#! /bin/bash
```

More examples are via `head -n 1 /etc/init.d/*`.

> The optional‑arg should either not be included or it should be a string that is meant to be a single argument (for reasons of portability, it should not contain any whitespace). - [Shebang_(Unix)][shebang]

We also frequently see the following shebang:

```shell
#!/usr/bin/env bash
```

That has to do with portability. Not every system install `node` or `bash` command in the same path. `/usr/bin/env` will search through user's `$PATH`, and correctly locate the executable.

To conclude for Node, here is the format I am using:

```shell
#! /usr/bin/env node

// Title
// =====
//
// Markdown style description starts here.

'use strict';
```

[shebang]: http://en.wikipedia.org/wiki/Shebang_(Unix)
[abs]: http://tldp.org/LDP/abs/html/sha-bang.html
