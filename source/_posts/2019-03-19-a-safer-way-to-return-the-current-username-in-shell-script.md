title: A Safer Way to Return the Current Username in Shell Script
date: 2019-03-19T12:00:00
categories: [Coding]
tags: [username, whoami, id, shell]
---

Getting the current username, it sounds like a very easy task:

```sh
$ bash -c 'echo "$USER"'
chao
```

But depending on the environment variable is not reliable, it can be easily overridden:

```sh
$ USER=foo bash -c 'echo "$USER"'
foo
```

Or just simply fail to work:

```sh
$ docker run --rm bash:4.4.23 bash -c 'USER=foo echo "$USER"'

```

The `USER` environment was not set in `/etc/profile` in this case:

```sh
$ docker run --rm bash:4.4.23 bash -c 'cat /etc/profile'
export CHARSET=UTF-8
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
export PAGER=less
export PS1='\h:\w\$ '
umask 022
```

This is especially important when writing shell script. You do not want to end up with a wrong username.

One solution is to use `whoami`, which prints the effective user:

```sh
$ USER=foo bash -c 'whoami'
chao
```

Another solution is to use `id` command:

```sh
$ USER=foo bash -c 'id -un'
chao
```

This will eliminate the overridden environment variable issue. Also, check out this StackOverflow post for others and POSIX compatible means:

<https://stackoverflow.com/questions/19306771/get-current-users-username-in-bash>

<!-- more -->

Test on different shells:

```sh
$ docker run --rm bash:4.4.23 bash -c 'USER=foo echo $(whoami)'
root
$ docker run --rm bash:4.4.23 bash -c 'USER=foo echo $(id -un)'
root
```

And Ash:

```sh
$ docker run --rm bash:4.4.23 ash -c 'USER=foo echo $(whoami)'
root
$ docker run --rm bash:4.4.23 ash -c 'USER=foo echo $(id -un)'
root
```
