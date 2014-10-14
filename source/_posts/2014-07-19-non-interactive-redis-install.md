title: Non-Interactive Redis Install
date: 2014-07-19 21:13:44
categories: [Tools]
tags: [redis, install, interactive, shell, read]
---

To build [Redis] from source, we first need to install TCL 8.5 or newer, this is needed for `make test` later:

    $ sudo apt-get install -y tcl

Now clone and make:

    $ git clone https://github.com/antirez/redis
    $ git checkout 2.8.13
    $ make
    $ make test
    $ sudo make install
    $ sudo utils/install_server.sh

Binary (`redis-cli` and `redis-server`) will be installed into `/usr/local/bin`.

The last command with `utils/install_server.sh` is an interactive command. Since the script is a shell script using the `read` built-in command and `-p` option for prompt, we can make it non-interactive by redirecting input from `echo` command:

    $ echo -n | sudo utils/install_server.sh

Without pumping any value into the script, the default values are used.

If really want to customize it, we can add our own values:

    $ echo -e \
      "${PORT}\n${CONFIG_FILE}\n${LOG_FILE}\n${DATA_DIR}\n${EXECUTABLE}\n" | \
      sudo utils/install_server.sh

There are 6 read statements, hence `n - 1` newline characters. Without using `-n`, the last newline character is supplied by `echo`.

Here are the default values:

    PORT=6379
    CONFIG_FILE=/etc/redis/6379.conf
    LOG_FILE=/var/log/redis_6379.log
    DATA_DIR=/var/lib/redis/6379
    EXECUTABLE=/usr/local/bin/redis-server

The `utils/install_server.sh` script should return something like this:

```
Welcome to the redis service installer
This script will help you easily set up a running redis server

Selecting default: 6379
Selected default - /etc/redis/6379.conf
Selected default - /var/log/redis_6379.log
Selected default - /var/lib/redis/6379
Selected config:
Port           : 6379
Config file    : /etc/redis/6379.conf
Log file       : /var/log/redis_6379.log
Data dir       : /var/lib/redis/6379
Executable     : /usr/local/bin/redis-server
Cli Executable : /usr/local/bin/redis-cli
Copied /tmp/6379.conf => /etc/init.d/redis_6379
Installing service...
 System start/stop links for /etc/init.d/redis_6379 already exist.
Success!
/var/run/redis_6379.pid exists, process is already running or crashed
Installation successful!
```

Set an alias for Redis client:

    $ cd /usr/local/bin && sudo ln -s redis-cli redis

For more advanced install, see the `README` file.

[Redis]: http://redis.io/
