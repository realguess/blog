title: Poor Man's VPN
date: 2013-11-24 23:22:24
tags: [sshuttle, vpn, proxy]
---

[sshuttle][] is a very simple tool to create a VPN connection to a remote server that you have SSH access. You do not need to set up the complex VPN in a remote machine.

Requirements:

- Root access on local machine
- SSH access to a remote machine

Install:

    $ sudo apt-get install sshuttle

Usage:

    $ sshuttle --dns -vvr username@sshserver 0/0

`0/0` is the shortcut for `0.0.0.0/0`. `--dns` enables DNS queries to be proxied. Note that `sudo` is not needed but will be prompted.

[sshuttle]: https://github.com/apenwarr/sshuttle/
