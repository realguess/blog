title: Install Docker on Ubuntu Trusty 14.04
date: 2015-09-07 12:00:00
categories: [DevOps]
tags: [install, docker, ubuntu, trusty]
---

Install [Docker] on [Ubuntu] Trusty 14.04 is fairly straightforward:

```sh
$ curl -sSL https://get.docker.com/ | sh
$ sudo usermod -aG docker ${USER}
$ exit
```

Log out, then log back in to verify the installation by running a sample container:

```sh
$ docker run hello-world
```

Done!

[Docker]: https://www.docker.com/
[Ubuntu]: http://www.ubuntu.com/
