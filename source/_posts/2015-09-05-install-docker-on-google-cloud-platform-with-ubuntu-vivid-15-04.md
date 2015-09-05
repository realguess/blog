title: Install Docker on Google Cloud Platform with Ubuntu Vivid 15.04
date: 2015-09-05 12:00:00
categories: [DevOps]
tags: [install, docker, ubuntu, vivid, systemd, google-compute-engine, google-cloud-platform]
---

[Ubuntu] 15.04 (Vivid Vervet) has switched the init manager from [Upstart] to [systemd]. Will the [Docker] installation change? Let's give a try and install it on a Google Compute Engine instance.

Install Docker:

```sh
$ curl -sSL https://get.docker.com/ | sh
```

Add user to Docker group for running commands without sudo:

```sh
$ sudo usermod -aG docker ${USER}
```

Log out, then log back in.

Verify the installation:

```sh
$ docker run hello-world

Hello from Docker.
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
1. The Docker client contacted the Docker daemon.
2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
$ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker Hub account:
https://hub.docker.com

For more examples and ideas, visit:
https://docs.docker.com/userguide/
```

For upgrading, the process is the same as installing:

```sh
$ curl -sSL https://get.docker.com/ | sh
```

<!-- more -->

Here are the installed info:

```sh
$ docker version
Client:
Version:      1.8.1
API version:  1.20
Go version:   go1.4.2
Git commit:   d12ea79
Built:        Thu Aug 13 02:40:42 UTC 2015
OS/Arch:      linux/amd64

Server:
Version:      1.8.1
API version:  1.20
Go version:   go1.4.2
Git commit:   d12ea79
Built:        Thu Aug 13 02:40:42 UTC 2015
OS/Arch:      linux/amd64
```

Google stuff:

```sh
$ gcloud version
Google Cloud SDK 0.9.37

bq 2.0.18
bq-nix 2.0.18
compute 2014.11.25
core 2014.11.25
core-nix 2014.11.25
dns 2014.11.25
gcutil 1.16.5
gcutil-nix 1.16.5
gsutil 4.6
gsutil-nix 4.6
sql 2014.11.25
```

Kernel:

```sh
$ uname -r
3.19.0-26-generic
```

OS:

```sh
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 15.04
Release:        15.04
Codename:       vivid
```

Related:

1. [Install Docker and then Run Docker Commands without Sudo](/2015/02/11/install-docker-and-then-run-docker-commands-without-sudo/)

[Ubuntu]: http://www.ubuntu.com/
[systemd]: https://en.wikipedia.org/wiki/Systemd
[Upstart]: https://en.wikipedia.org/wiki/Upstart
[Docker]: https://www.docker.com/
