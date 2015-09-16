title: Wait and then Grab the Lock and Update
date: 2015-09-16 12:00:00
categories: [DevOps]
tags: [update, apt-get, lock, vagrant, google-compute-engine, gce]
---

When provisioning a new machine in [Google Compute Engine] with [Vagrant], I spotted the following error messages when updating the system:

```sh
Could not get lock /var/lib/apt/lists/lock - open (11: Resource temporarily unavailable)
E: Unable to lock directory /var/lib/apt/lists/
```

It appears to be that I was just behind on the race to grab the lock on the APT update. Without getting the latest index, I am not able to upgrade the packages. An simple solution is to wait it out:

```sh
#!/usr/bin/env bash
#
# System Update
# =============

apt-get update
while [ "$?" != "0" ]; do
  sleep 5
  apt-get update
done
apt-get -y dist-upgrade
```

[Vagrant]: https://www.vagrantup.com/
[Google Compute Engine]: https://cloud.google.com/compute/
