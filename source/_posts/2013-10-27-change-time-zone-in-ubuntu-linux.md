title: Change Time Zone in Ubuntu Linux
date: 2013-10-27 10:39:40
tags: [time-zone, ubuntu, linux]
---

All time zone files are located:

    /usr/share/zoneinfo

Locate the timezone file name such as `Asia/Hong_Kong`:

Change the time zone as root:

    echo 'Asia/Hong_Kong' > /etc/timezone 

Configure time zone data:

    dpkg-reconfigure --frontend noninteractive tzdata

Sync clock:

    ntpdate pool.ntp.org
