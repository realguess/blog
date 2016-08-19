title: Node.js Installation Methods July 2016 Edition
date: 2016-07-05T12:00:00  
categories: [Coding]  
tags: [install, node, nodesource, dockerfile]

---

Follow up with the [previous blog][1] on various methods to install [Node.js], here is the updated one with Node.js v4.x and v6.x versions.

Install Node.js v4.x via [NodeSource][][^1] setup script:

```
$ curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```

`sudo -E` option indicates to the security policy that the user wishes to preserve their existing environment variables[^2].

With [Docker] and [Dockerfile], `sudo` should be removed, because root:

```
RUN curl -sL https://deb.nodesource.com/setup_4.x | bash - && \
    apt-get install -y nodejs
```

Install Node.js v6.x via the same method:

```
$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```

The above installation method has been tested on:

- Debian 8.5
- Node.js v4.4.7 and v6.2.2

In summary, the methods to install Node.js covered are:

- Package manager
- Nodesource script
- From the source


[^1]: NodeSource provides [binary distribution setup and support scripts][2].
[^2]: See `man sudo`.
[1]: https://realguess.net/2015/09/02/various-node-js-installation-methods/
[2]: https://github.com/nodesource/distributions
[Docker]: https://www.docker.com/
[Dockerfile]: https://docs.docker.com/engine/reference/builder/
[Node.js]: https://nodejs.org/
[NodeSource]: https://nodesource.com/
