title: sudo -s or sudo -i
date: 2019-01-07T12:00:00
categories: [Computing]
tags: [sudo]
---

sudo allows users to run programs with the security privileges of another user (superuser or other users). Of the supported options, what's difference between `-s` and `-i`?

Both options run an interactive shell if no command is specified:

```sh
$ sudo -s
$ sudo -i
```

The difference is that when using `-i`:

> sudo attempts to change to that user's home directory before running the shell. The command is run with an environment similar to the one a user would receive at log in. - `man sudo`

Let's assume the current directory is at `/`:

```sh
$ sudo -s pwd
/

$ sudo -i pwd
/root
```

Changing directory is not attempted when using `-s` option.

sudo is commonly used to elevate the privilege to execute as the superuser, and usually done in place rather than in the superuser's home directory, such as:

```sh
$ sudo -s chown $USER:$GROUP file
```

Therefore, when running as superuser, use `-s`:

```sh
$ sudo -s
```

When running as another user, use `-i`:

```sh
$ sudo -u foo -i
```
