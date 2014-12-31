title: Revoke and Grant Public IP Addresses to Amazon EC2 Instances Via AWS Command Line Interface (CLI)
date: 2014-12-27 12:00:00
categories: [Tools]
tags: [aws-cli, cli, security-group, ec2, aws, amazon, automation, ip-address]
---

If you work from place to place, such as from one coffee shop to another, and you need access to your Amazon EC2 instances, but you don't want to allow traffics from all IP addresses. You can use the EC2 [Security Groups] to allow the IP addresses from those locations. But once you move on to a different location, you want to delete the IP address from the previous location. The process to do these manually and over and over again quickly becomes cumbersome. Here is a command line method that quickly removes all other locations and allows only the traffic from your current location.

The steps are:

1. Revoke all existing sources to a particular port
2. Grant access to the port only from the current IP address

Assume the following:

- Profile: `default`
- Security group: `mygroup`
- Protocol: `tcp`
- Port: `22`

First, revoke access to the port from all IP addresses:

```sh
$ aws ec2 describe-security-groups \
    --profile default \
    --group-names mygroup \
    --query 'SecurityGroups[0].IpPermissions[?ToPort==`22`].IpRanges[].CidrIp' | \
  jq .[] | \
  xargs -n 1 aws ec2 revoke-security-group-ingress \
    --profile default \
    --group-name mygroup \
    --protocol tcp \
    --port 22 \
    --cidr
```

The `aws ec2 describe-security-groups` command before the first pipe returns JSON formatted data, filtered via [JMESPath] query, which is supported by [AWS CLI], for example:

```
[
  "XXX.XXX.XXX.XXX/32",
  "XXX.XXX.XXX.XXX/32"
]
```

[jq] command simply converts an array of JSON to line by line strings, which `xarg` takes in, loops through and deletes one IP address at a time.

After this step, all IP addresses originally allowed are all revoked. Next step is to grant access to the port from a single IP address:

<!-- more -->

```sh
$ aws ec2 authorize-security-group-ingress \
    --profile default \
    --group-name mygroup \
    --protocol tcp \
    --port 22 \
    --cidr $(curl -s ipinfo.io/ip)/32
```

Getting your public IP address can be done by querying the web service:

```sh
$ curl ipinfo.io/ip
```

You can replace it with another service, such as:

```sh
$ curl -s www.trackip.net/ip
```

Use `-s` or silent mode to prevent progress meter or error messages from showing.

You can write a shell script or command alias to put both revoking and granting actions together.

This is good for a single user. If you have more than one user accessing the instances belong to the same security group, then it is better to try other approaches, such as tunneling through another server.

[Security Groups]: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html
[AWS CLI]: http://aws.amazon.com/cli/
[JMESPath]: http://jmespath.org/
[jq]: http://stedolan.github.io/jq/
