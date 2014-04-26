title: Defining Correct Environment for CoffeeScript in Cron
date: 2014-02-11 22:42:55
tags: [cron, crontab, node, coffeescript, environment, path]
---

I have written a bunch of CoffeeScript scripts that I would like to run them regularly. [Cron][] is certainly the right choice in many situation.

[cron]: http://en.wikipedia.org/wiki/Cron

The default executable path recognized by cron is limited:

    * * * * * echo $PATH > /tmp/log/cron.log

This cron job will recognize the following path:

    /usr/bin:/bin

However the default executable path for Node.js is usually at:

    $ which node
    /usr/local/bin/node

Therefore, if you attempt to run a cron job:

    * * * * * node -v > /tmp/log/cron.log 2> /tmp/log/cron.err.log

It will return an error, where the executable is not found:

    bin/sh: 1: node: not found

The easiest way to solve it is by specifying the fully qualified path:

    * * * * * /usr/local/bin/node -v > /tmp/log/cron.log

To ensure the process is installed, we can check the file existence and permission before launching it:

    * * * * * test -x /usr/local/bin/node && /usr/local/bin/node -v

But when working with CoffeeScript, the same method does not working:

    * * * * * /usr/local/bin/coffee -v 2> /tmp/log/cron.err.log

Because CoffeeScript binary (`coffee`) use the following format:

    #!/usr/bin/env node

Which does not specify fully qualified path on the shebang line, and will result in:

    /usr/bin/env: node: No such file or directory

We can compile CoffeeScript into JavaScript and use fully qualified `node` executable. But this is not necessary. Since we are using Debian based system, which uses Vixie cron. It allows environment variables to be defined. We can make Cron aware of the custom path in environment variable:

    * * * * * PATH=$PATH:/usr/local/bin coffee -v > /tmp/log/cron.log

Now the executable path include the one where both `node` and `coffee` commands reside.

We can also move it to its own line. But Cron is not shell, it does not expand the variable, so you have to specify all paths:

    # Error: `PATH=$PATH:/usr/local/bin`
    PATH=/usr/bin:/bin:/usr/local/bin
    * * * * * coffee -v > /tmp/log/cron.log

The good practice is to set environment variables in a script:

```
#!/usr/bin/env bash
#
# This script is intended to be run as a cron job.

PATH=$PATH:/usr/local/bin
coffee -v
```

and run the script as a cron job:

    * * * * * /path/to/script > /tmp/log/cron.log 2>> /tmp/log/cron.err.log

