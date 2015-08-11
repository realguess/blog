title: Restart Upstart Instances on System Reboot
date: 2014-11-23 12:00:00
categories: [Coding]
tags: [upstart, restart, reboot, multiple]
---

A single [Upstart] job can have multiple instances running:

```
$ sudo start my-job port=4000
$ sudo start my-job port=4001
$ sudo start my-job port=4002
```

However, when the operating system reboots, the job with multiple instances will fail to start, due to instance information is not provided to the job. We can fix this problem by adding a `for` loop in the `script` section:

```sh
start on (local-filesystems and net-device-up IFACE!=lo)
stop  on shutdown

script
  for i in `seq 4000 4002`
  do
    exec /path/to/my/job
  done
end script
```

With this Upstart job, we do not need to provide instance information:

```
$ sudo start my-job
```

Therefore, during system restart, the job will initiate automatically.

<!-- more -->

If you want more control over the individual instance, you can create two types Upstart jobs: master and slave. Master job is configured to start during system reboot, and it will kick off all slave job instances at the `script` section, instead of executing the actual job:

```sh
start on (local-filesystems and net-device-up IFACE!=lo)
stop  on shutdown

script
  for i in `seq 4000 4002`
  do
    start my-job-instance port=$i
  done
end script
```

And for the slave job with the `instance` tag:

```sh
stop on shutdown

instance $port

script
  exec /path/to/my/job
end script

respawn
respawn limit 10 5
```

This will provide the flexibility to turn on and off individual instance at the same time being able to restart all instances at the system reboot.

[Upstart]: http://upstart.ubuntu.com/
