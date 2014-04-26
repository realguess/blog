Cloud Init on EC2
=================

cloud-init:

Why need to know `cloud-init`? Because you want to customize the vanilla image.

If you use one of the official [Ubuntu images][] in Amazon EC2, [cloud-init][1] is installed. It handles early initialization of a cloud instance. Issue the following command on a running instance with official Ubuntu image:

    dpkg -l cloud-init

You should see something similar:

```
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version        Description
+++-==============-==============-============================================
ii  cloud-init     0.6.3-0ubuntu1 Init scripts for cloud instances
```

If you need to run some special scripts during initialization of the instance, you can configure it via user data.

> You can specify user data to configure an instance or run a configuration script during launch. If you launch more than one instance at a time, the user data is available to all the instances in that reservation

There can be more than one user data formats that are accepted by cloud-init, see the documentation for more information.

```
#!/usr/bin/env bash

echo "Hello World. The time is now $(date -R)!" | tee /root/output.txt
```

Will you see the message in both the local screen if you use the data script? And use the command line tool?

Do I need to upgrade the Amazon command line first to the latest version? And when writing script and make sure to describe it as one of requirements.

> user-data scripts are a convenient way to do something on first boot of a launched instance [1]
> user-data is limited to 16384 bytes [1]

Is it user data is something to run when you first launch the instance, so you don't need to log on to the instance and upload your instance setup script to the newly created instance, all can be done via user data by downloading the script from Amazon S3, and execute it.

> Cloud Config is the simplest way to accomplish some things via user-data. [1]
> a different apt mirror should be used [1]

I like this one, because I need to use Singapore mirror image, which will be much faster than access US images.

I am loving it, `cloud-config` supports YAML format.

Why we need to use [write-mime-multipart][] in EC2? Due to use of user data?

Because sometimes you need to include both user script and Upstart Job, if you don't, you probably don't need to use multi-part.

But if you just want to get `write-mime-multipart`, you need to have `cloud-utils` installed:

    sudo apt-get install cloud-utils

[ubuntu images]: http://cloud-images.ubuntu.com/releases/
[write-mime-multipart]: http://manpages.ubuntu.com/manpages/raring/man1/write-mime-multipart.1.html
[1]: https://help.ubuntu.com/community/CloudInit
