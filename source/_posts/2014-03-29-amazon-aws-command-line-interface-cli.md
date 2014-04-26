title: Amazon AWS Command Line Interface (CLI)
date: 2014-03-29 10:44:53
tags: [aws-cli, command-line-interface, cli, aws]
---

This is a brief guide for installing [AWS Command Line Interface (CLI)][cli] on Ubuntu Linux.

> The AWS Command Line Interface (CLI)] is a unified tool to manage your AWS services. With just one tool to download and configure, you can control multiple AWS services from the command line and automate them through scripts. [2]

The point here is **unified**, one tool to run all Amazon AWS services.


Install
-------

_The installation procedure applies to Ubuntu Linux with Zsh and Bash._

Install [pip][], a Python package manager:

    $ sudo apt-get install python-pip

Install _awscli_:

    $ sudo pip install awscli

Install autocompletion:

    $ which aws_zsh_completer.sh
    /usr/local/bin/aws_zsh_completer.sh
    $ source aws_zsh_completer.sh

Add this line to `~/.zshrc` as well.

Or for Bash `~/.bashrc`:

    $ echo '\n# Enable AWS CLI autocompletion' >> ~/.bashrc
    $ echo 'complete -C aws_completer aws' >> ~/.bashrc
    $ source ~/.bashrc

Test installation:

    $ which aws
    /usr/local/bin/aws
    $ aws help

Test autocompletion:

    $ aws <tab><tab>

You should see a list of all available AWS commands.


Usage
-----

Before using aws-cli, you need to tell it about your AWS credentials. There are three ways to specify AWS credentials:

1. Environment variables
2. Config file
3. IAM Role

Using config file is preferred, which is a simple [ini] file format to be stored in `~/.aws/config`. A soft link can be used to link it or just tell awscli where to find it:

    $ export AWS_CONFIG_FILE=/path/to/config_file

It is better to use IAM roles with any of the AWS services:

> The final option for credentials is highly recommended if you are using aws-cli on an EC2 instance. IAM Roles are a great way to have credentials installed automatically on your instance. If you are using IAM Roles, aws-cli will find them and use them automatically. [4]

The default output is in JSON format. Other formats are tab-delimited text and ASCII-formatted table. For example, using `--query` filter and table output:

    $ aws ec2 describe-instances --query 'Reservations[*].Instances[*].{ \
      ID:InstanceId, TYPE:InstanceType, ZONE:Placement.AvailabilityZone, \
      SECURITY:SecurityGroups[0].GroupId, KEY:KeyName, VPC:VpcId, \
      STATE:State.Name}' --output table

This will print a nice looking table of all EC2 instances.

The command line options also accept JSON format. But when passing in large blocks of data, referring a JSON file is much easier. Both local file and remote URL can be used.


Upgrade
-------

To upgrade AWS CLI to the latest version:

    $ sudo pip install --upgrade awscli


References
----------

1. [AWS Command Line Interface][cli]
2. [User Guide][userguide]
3. [Reference][reference]
4. [GitHub repository][github]


[cli]: http://aws.amazon.com/cli/
[userguide]: http://docs.aws.amazon.com/cli/latest/userguide/
[reference]: http://docs.aws.amazon.com/cli/latest/reference/
[github]: https://github.com/aws/aws-cli/
[pip]: http://www.pip-installer.org/en/latest/
[ini]: http://en.wikipedia.org/wiki/INI_file
