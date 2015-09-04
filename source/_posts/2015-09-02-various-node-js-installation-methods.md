title: Various Node.js Installation Methods
date: 2015-09-02 12:00:00
categories: [JavaScript]
tags: [install, node, iojs]
---

From [io.js] back to [Node.js], time to update my installation guide.


## Install via Package Manager

Install in Ubuntu directly via apt:

```sh
$ sudo apt-get install -y nodejs
```

However, it will not give you the latest version. So, use an alternative method.

For Node.js v0.12:

```sh
$ curl -sL https://deb.nodesource.com/setup_0.12 | sudo bash -
$ sudo apt-get install -y nodejs
```

For io.js v3.x:

```sh
$ curl -sL https://deb.nodesource.com/setup_iojs_3.x | sudo bash -
$ sudo apt-get install -y iojs
```


## Install from the Source

Install build tools:

```sh
$ sudo apt-get install -y build-essential
```

Clone the Node.js repository from GitHub:

```sh
$ git clone https://github.com/nodejs/node.git

```

Future Node.js releases will be from this repo: <https://github.com/nodejs/node>.

Check out a specific version:

```sh
$ cd node && git checkout v3.2.0
```

Install globally:

```sh
$ ./configure && make && sudo make install
```

[io.js]: https://iojs.org/
[Node.js]: https://nodejs.org/
