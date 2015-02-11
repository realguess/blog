title: How to Correctly Use Environment Variables on the Command Line
date: 2015-02-08 12:00:00
categories: [Linux]
tags: [shell-expansion, environment-variable, shell, cli]
---

I always forget about how to use shell environment variable correctly. I think because I did not grasp the key concept. For example, here is the wrong approach:

```sh
$ FOO=foo BAR=bar echo foo is ${FOO} and bar is ${BAR}
foo is and bar is
```

The correct answer I was hoping for is:

```sh
foo is foo and bar is bar
```

Because what I frequently do is use environment variable inside shell script:

```sh
#!/usr/bin/env bash
#
# script.sh

echo foo is ${FOO} and bar is ${BAR}
```

then do:

```sh
$ FOO=foo BAR=bar ./script.sh
foo is foo and bar is bar
```

This works as expected because the environment variables are passed into the subshell that executing the script.

But why not the first or the wrong approach?

The reason is that **shell expands the variable before the command being executed**. To shell, the first approach looks like:

```sh
$ FOO=foo BAR=bar echo foo is and bar is 
```

The environment variables `FOO` and `BAR` were expanded before the command was executed. Hence, no values were printed.

<!-- more -->

Creating a shell script is a method, but here are three more solutions and better ones:

```sh
$ FOO=foo BAR=bar; echo foo is ${FOO} and bar is ${BAR}
```

or replace the semicolon with boolean operator:

```sh
$ FOO=foo BAR=bar && echo foo is ${FOO} and bar is ${BAR}
```

or use a subshell:

```sh
$ FOO=foo BAR=bar sh -c 'echo foo is ${FOO} and bar is ${BAR}'
```

So, the key is that **shell expands the environment variables before executing the command**.
