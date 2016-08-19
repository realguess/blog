title: Fixing Authorization Failure in AWS CLI by Synchronizing the Clock
date: 2016-08-09 12:00:00
categories: [Computing]
tags: [aws-cli, cli, command-line, aws, ntpdate, ntp]
---

Running into an error when executing an [AWS] command:

```
$ aws ec2 describe-instances

An error occurred (AuthFailure) when calling the DescribeInstances operation: AWS
was not able to validate the provided access credentials
```

From the error message, it appears to be an error with access credentials. But after updating to a new credential, and even updated the AWS package, the error still persisted. After trying out other commands, there was an error message containing "signature not yet current" with timestamps. So, the actual problem was due to inaccurate local clock. Hence, the solution is to sync the local date and time by polling the Network Time Protocol (NTP) server:

```
$ sudo ntpdate pool.ntp.org
```

> ntpdate can be run manually as necessary to set the host clock, or it can be run from the host startup script to set the clock at boot time.  This is useful  in some cases to set the clock initially before starting the NTP daemon ntpd. It is also possible to run ntpdate from a cron script. However, it is important to note that ntpdate with contrived cron scripts is no substitute for the NTP daemon, which uses sophisticated algorithms to maximize accuracy  and  reliability while minimizing resource use. Finally, since ntpdate does not discipline the host clock frequency as does ntpd, the accuracy using ntpdate is limited.[^1]

From the description, we can learn that we can make things even easier by installing NTP package:

```
$ sudo apt-get install -y ntp
```

> Network Time Protocol daemon and utility programs NTP, the Network Time Protocol, is used to keep computer clocks accurate by synchronizing them over the Internet or a local network, or by following an accurate hardware receiver that interprets GPS, DCF-77, NIST or similar time signals.[^2]

Verify the installation and execution:

```
$ ps -e | grep ntpd
 4964 ?        00:00:00 ntpd
```

with the environment:

```
$ aws --version
aws-cli/1.10.53 Python/2.7.6 Linux/3.13.0-92-generic botocore/1.4.43
```


[^1]: `$ man nptdate`
[^2]: `$ apt-cache show ntp`
[AWS]: https://aws.amazon.com/
