title: Passing Environment Variables from Cron to Shell Script
date: 2014-03-29 22:58:55
tags: [environment-variable, cron, shell-script]
---

In Debian system with Vixie cron, [environment variables can be defined][env-def] in the crontab:

    MESSAGE=Hello World
    * * * * * /bin/sh ~/script.sh >> /tmp/script.log

Now, can the same environment variable `MESSAGE` be passed to the shell script `~/script.sh` that being scheduled to run every minute? Let's just give a try by adding the following line to `script.sh`:

    echo "ECHO: ${MESSAGE}"

`tail -f /tmp/script.log`:

    ECHO: Hello World
    ECHO: Hello World
    ECHO: Hello World

Therefore, the shell script will pick up the environment variables defined in crontab. This really is a convenience.

[env-def]: /2014/02/11/defining-correct-environment-for-coffeescript-in-cron/
