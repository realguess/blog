title: Install Docker and then Run Docker Commands without Sudo
date: 2015-02-11 12:00:00
categories: [DevOps]
tags: [docker, install, sudo]
---

Commands:

```sh
$ curl -sSL https://get.docker.io/ubuntu/ | sudo sh
$ sudo usermod -a -G docker $USER
$ sudo service docker restart
$ logout
```

Explanation:

1. One line command installation
2. Add the user to the group `docker`
3. Restart the daemon
4. Make sure to logout and log back in, in order to sudoless Docker taking effect.
