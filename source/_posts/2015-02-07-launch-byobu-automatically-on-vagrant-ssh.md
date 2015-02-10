title: Launch Byobu Automatically on Vagrant SSH
date: 2015-02-07 12:00:00
categories: [Tools]
tags: [byobu, tmux, vagrant, ssh]
---

I can [use SSH directly instead of Vagrant SSH command][1] for interactive programs such as `top` or running `tmux` sessions. But I frequently just want to run [Tmux] or [Byobu] upon login. I can do:

```sh
$ ssh -F ssh-config vagrant -t byobu
```

But still too much trouble to go through all SSH configuration steps. So, I ended up with two-step process:

```sh
$ vagrant ssh
```

Now inside the guest machine, I immediately type:

```sh
$ byobu
```

To bring up new Tmux session or attach existing sessions.

<!-- more -->

Luckily, I just learned that I can collapse these two commands into a single one by enabling Byobu upon login. This is done by running the following command from the guest machine:

```sh
$ byobu-enable
```

This will add a line to your `$HOME/.profile` file which launches Byobu any time you login via SSH or a console:

```bash
_byobu_sourced=1 . /usr/bin/byobu-launch
```

If you need to run something other interactive process, we can disable the behavior from the guest machine:

```sh
$ byobu-disable
```

Another day another quick time saving trick.

[1]: /2014/12/10/use-ssh-directly-instead-of-vagrant-ssh-command/
[Vagrant]: https://www.vagrantup.com/
[Tmux]: http://tmux.sourceforge.net/
[Byobu]: http://byobu.co/
