title: Use CNAME for Resovling Private and Public IP Address in Amazon EC2
date: 2014-10-25 15:37:25
categories: [Cloud]
tags: [dns, cname, security-group, ec2, route-53, aws, amazon, firewall]
---

"A security group acts as a virtual firewall that controlls the traffic for one or more instances." [1] The [Amazon EC2][] [Security Groups][1] are not just capable controlling traffic from an IP address, but also from all EC2 instances belong to a specific security group. I want to allow an instance belonging to one security group to access an instance belongs to another security group via a custom domain name (subdomain.example.com).

But when I configured the subdomain via [Amazon Route 53], I have misconfigured it by assigning a A record, an IP address or the [Elastic IP address][EIP] of the instance. I should have used CNAME, and assigned the public DNS (the public hostname of the EC2 instance).

<!-- more -->

> Incoming traffic is allowed based on the private IP addresses of the instances that are associated with the source security group (and not the public IP or Elastic IP addresses). [1]

So, when accessing the EC2 instance from another instance via the domain name, the access was denied. Because the public IP address is recognized, not the internal IP. You can assign a rule in security group to allow access from the public IP address from the instance, but if you examine your access log:

```
Address xx.xxx.xxx.xxx maps to ec2-xx-xx-xxx-xxx.compute-1.amazonaws.com,
but this does not map back to the address - POSSIBLE BREAK-IN ATTEMPT!
```

> We resolve an external DNS hostname to the public IP address of the instance outside the network of the instance, and to the private IP address of the instance from within the network of the instance. [2]

So, the insance resolves the internal IP, which does not match the external IP, hence the _possible break-in attempt_.

The problem can be fixed by assigning [CNAME][] instead of IP address. Also, see [Using Elastic IP to Identify Internal Instances on Amazon EC2][3] by Eric Hammond for additional information on using Elastic IP.

In Amazon Route 53, I have changed the record from:

```
name: subdomain.example.com
type: A (IPv4 address)
value: xx.xx.xxx.xx
```

to:

```
name: subdomain.example.com
type: CNAME (Canonical name)
value: ec2-xx-xx-xxx-xx.compute-1.amazonaws.com
```

After the update, when resolving from an EC2 instance, it will lead to private IP. But from the external point of view, it will resolve to the public IP or the assigned Elastic IP. Now I am able to access the instance belongs to another security group via a custom domain name (subdomain.example.com).


# References

1. [Amazon EC2 Security Groups][1]
2. [Amazon EC2 Instance IP Addressing][2]
3. [Using Elastic IP to Identify Internal Instances on Amazon EC2][3]

[1]: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html
[2]: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html
[3]: http://alestic.com/2009/06/ec2-elastic-ip-internal
[EIP]: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html
[Amazon EC2]: https://aws.amazon.com/ec2/
[Amazon Route 53]: https://aws.amazon.com/route53/
[CNAME]: https://en.wikipedia.org/wiki/CNAME_record
