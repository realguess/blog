title: Registering a Domain with Amazon Route 53 via CLI
date: 2016-12-30 12:00:00
categories: [DevOps]
tags: [domain, route-53, aws, awscli, cli, jq, xargs]
---

Register a domain with [Amazon Route 53] via CLI is very straightforward, the only complicate thing is to generate the acceptable JSON structure, then somehow enter the JSON string in the command line without meddling from the shell.

The command is:

```
$ aws route53domains register-domain
```

The documentation can be found from the [AWS CLI Command Reference][1] or issuing:

```
$ aws route53domains register-domain help
```

Most the command line options require simple string values, but some options like `--admin-contact` requires a structure data, which is complex to enter in the command line. The easiest way is to forget all other options and use `--cli-input-json` to get everything in JSON.

First let's generate the skeleton or template to use:

```sh
$ aws route53domains register-domain --generate-cli-skeleton
```

That should output something like:

```json
{
    "DomainName": "",
    "IdnLangCode": "",
    "DurationInYears": 0,
    "AutoRenew": true,
    "AdminContact": {
        "FirstName": "",
        "LastName": "",
        "ContactType": "",
        "OrganizationName": "",
        "AddressLine1": "",
        "AddressLine2": "",
        "City": "",
        "State": "",
        "CountryCode": "",
        "ZipCode": "",
        "PhoneNumber": "",
        "Email": "",
        "Fax": "",
        "ExtraParams": [
            {
                "Name": "",
                "Value": ""
            }
        ]
    },
    "RegistrantContact": {
        "FirstName": "",
        "LastName": "",
        "ContactType": "",
        "OrganizationName": "",
        "AddressLine1": "",
        "AddressLine2": "",
        "City": "",
        "State": "",
        "CountryCode": "",
        "ZipCode": "",
        "PhoneNumber": "",
        "Email": "",
        "Fax": "",
        "ExtraParams": [
            {
                "Name": "",
                "Value": ""
            }
        ]
    },
    "TechContact": {
        "FirstName": "",
        "LastName": "",
        "ContactType": "",
        "OrganizationName": "",
        "AddressLine1": "",
        "AddressLine2": "",
        "City": "",
        "State": "",
        "CountryCode": "",
        "ZipCode": "",
        "PhoneNumber": "",
        "Email": "",
        "Fax": "",
        "ExtraParams": [
            {
                "Name": "",
                "Value": ""
            }
        ]
    },
    "PrivacyProtectAdminContact": true,
    "PrivacyProtectRegistrantContact": true,
    "PrivacyProtectTechContact": true
}
```

Fill in the fields, leave out the ones not needed:

```json
{
    "DomainName": "example.com",
    "DurationInYears": 1,
    "AutoRenew": true,
    "AdminContact": {
        "FirstName": "Joe",
        "LastName": "Doe",
        "ContactType": "PERSON",
        "AddressLine1": "101 Main St",
        "City": "New York",
        "State": "NY",
        "CountryCode": "US",
        "ZipCode": "10001",
        "PhoneNumber": "+1.8888888888",
        "Email": "joe.doe@example.com"
    },
    "RegistrantContact": {
        "FirstName": "Joe",
        "LastName": "Doe",
        "ContactType": "PERSON",
        "AddressLine1": "101 Main St",
        "City": "New York",
        "State": "NY",
        "CountryCode": "US",
        "ZipCode": "10001",
        "PhoneNumber": "+1.8888888888",
        "Email": "joe.doe@example.com"
    },
    "TechContact": {
        "FirstName": "Joe",
        "LastName": "Doe",
        "ContactType": "PERSON",
        "AddressLine1": "101 Main St",
        "City": "New York",
        "State": "NY",
        "CountryCode": "US",
        "ZipCode": "10001",
        "PhoneNumber": "+1.8888888888",
        "Email": "joe.doe@example.com"
    },
    "PrivacyProtectAdminContact": true,
    "PrivacyProtectRegistrantContact": true,
    "PrivacyProtectTechContact": true
}
```

Now let's register by running through [jq] to clean up extra blanks and end of line characters, and then pipe to `xargs` command by using new line character as the delimiter instead of a blank by default:

```sh
$ cat input.json | jq -cM . | \
  xargs -d '\n' aws route53domains register-domain --cli-input-json
{
    "OperationId": "00000000-0000-0000-0000-00000000000"
}
```

<!--more-->

I have tried other options such as:

```sh
$ aws route53domains register-domain --cli-input-json $(cat input.json | jq -cM .)
```

But you will get an unknown options error, because there is a blank in the `AddressLine1` field. I wish the option can accept a file like `@input.json`, that will make it much easier, without worrying about quoting and JSON.

Finally, got email for verifying contact data:

> Hello,
>
> This is an important email, and your prompt action is required.  If you do not act on the instructions we are providing below, your domain name(s) will be suspended on 2016-12-31 12:00.
>
> Why is this required?
> Beginning January 1st, 2014, ICANN requires that registrars verify that domain contacts can be reached.
>
> What do you need to do?
> Please confirm that your email address <joe.doe@example.com> can be reached, by clicking on the link below:
>
> http://domainnameverification.net/?hash=hash
>
> If you use this link, we will not need to ask you for any password during this process.
>
> Amazon Route 53
>
> To setup your domain, log in to the AWS Console at https://console.aws.amazon.com/route53/home

Then comes the successful registration email:

> Hi,
>
> We have successfully registered example.com. We also created a hosted zone for your domain. This hosted zone is where you store information about how to route traffic for your domain, for example, to an Amazon EC2 instance or a CloudFront distribution. To learn how to add resource record sets to your hosted zone, see Working with Resource Record Sets.
>
> If you did not request this change, please contact Amazon Web Services Customer Support immediately.
>
> Amazon Route 53

Great! We're done!

Settings:

```
$ aws --version
aws-cli/1.11.34
```


[1]: https://docs.aws.amazon.com/cli/latest/reference/route53domains/register-domain.html
[Amazon Route 53]: https://aws.amazon.com/route53/
[jq]: https://stedolan.github.io/jq/
