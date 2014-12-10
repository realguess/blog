title: Use SSH Directly Instead of Vagrant SSH Command
date: 2014-12-10 12:00:00
categories: [Tools]
tags: [vagrant, ssh, interactive]
---

[Vagrant] command `vagrant ssh` connects to the running virtual machine via SSH. SSH arguments can also be added:

```plain
$ vagrant ssh -h
Usage: vagrant ssh [options] [name] [-- extra ssh args]

Options:

    -c, --command COMMAND            Execute an SSH command directly
    -p, --plain                      Plain mode, leaves authentication up to user
    -h, --help                       Print this help
```

For example, execute a single command:

```
$ vagrant ssh -c date
Wed Dec 10 12:00:00 UTC 2014
Connection to 127.0.0.1 closed.
```

or:

```
$ vagrant ssh -- date
Wed Dec 10 12:00:00 UTC 2014
```

which prints no connection closed message.

Another example:

```plain
$ vagrant ssh -- 'cat /etc/hosts'
127.0.0.1 localhost

# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts
```

However, interactive applications cannot be used in this way:

```plain
$ vagrant ssh -- top
TERM environment variable not set.
```

Well, we already have a virtual machine running, we can just use SSH directly.

<!-- more -->

Print the SSH configuration:

```sh
$ vagrant ssh-config
Host default
  HostName 127.0.0.1
  User vagrant
  Port 2222
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /home/chao/.vagrant.d/insecure_private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

Vagrant command `ssh-config` outputs SSH configuration to connect to the machine.

To provide a custom hostname instead of `default`:

```plain
$ vagrant ssh-config --host vagrant
Host vagrant
  HostName 127.0.0.1
```

Save this piece of information:

```plain
$ vagrant ssh-config --host vagrant > ssh-config
```

Now we can run interactive process via SSH command directly:

```plain
$ ssh -F ssh-config vagrant -t top
```

SSH `-t` option forces pseudo-tty allocation. So interactive programs such as `top` can be executed.

[Vagrant]: https://www.vagrantup.com/
