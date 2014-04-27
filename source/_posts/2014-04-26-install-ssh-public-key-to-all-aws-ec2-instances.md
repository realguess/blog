title: Install SSH Public Key to All AWS EC2 Instances
date: 2014-04-26 17:30:46
tags: [ssh-copy-id, ssh, ec2, aws]
---

I've got a new laptop, and I need to install the SSH public key of the new machine to all my AWS EC2 instances in order to enable keyless access. I can use `ssh-copy-id` to install the public key one instance at a time, but I can also do it all at once:

    $ aws ec2 describe-instances --output text \
      --query 'Reservations[*].Instances[*].{IP:PublicIpAddress}' \
      while read host; do \
      ssh-copy-id -i /path/to/key.pub $USER@$host; done

Somehow if using `PublicIpAddress`, some IP addresses in the response were cluttered in a single line. So, I use `{IP:PublicIpAddress}` instead.

For non-standard port:

    $ ssh-copy-id -i /path/to/key.pub "$USER@$host -p $port"

The only problem is that it might install a duplicate key in `~/.ssh/authorized_keys` file of the remote instance, if the key has already been installed. One way to solve this problem is to test the login from the new machine and generate only the IP addresses that the new machine does not have access to:

    $ aws ec2 describe-instances --output text \
      --query 'Reservations[*].Instances[*].{IP:PublicIpAddress}' \
      | while read host; do ssh -q $USER@$host exit \
      || echo $host; done > instances.txt


Now back to the old machine, install the public key at once:

    $ cat instances.txt | while read host; \
      do ssh-copy-id -i /path/to/key.pub $USER@$host; done
