title: Amazon Route 53 via Command Line
date: 2013-12-06 10:55:55
tags: [command-line, hosted-zone, record-set, aws, route-53, amazon]
---

Retrieve a list of hosted zones:

```
$ aws route53 list-hosted-zones          
{
  "HostedZones": [
    {
        "ResourceRecordSetCount": 4,
        "CallerReference": "12345678-ABCD-EFGH-IJKL-ABCDEFGHIJKL",
        "Config": {},
        "Id": "/hostedzone/1234567890ABC",
        "Name": "realguess.net."
    }
  ], 
  "IsTruncated": false, 
  "MaxItems": "100"
}
```

Get a single hosted zone with delegation set (four Route 53 name servers that were assigned to the hosted zone):

```
$ aws route53 get-hosted-zone --id 1234567890ABC
{
  "HostedZone": {
    "ResourceRecordSetCount": 4,
    "CallerReference": "12345678-ABCD-EFGH-IJKL-ABCDEFGHIJKL",
    "Config": {},
    "Id": "/hostedzone/1234567890ABC",
    "Name": "realguess.net."
  },
  "DelegationSet": {
    "NameServers": [
      "ns-1727.awsdns-23.co.uk",
      "ns-1312.awsdns-36.org",
      "ns-402.awsdns-50.com",
      "ns-587.awsdns-09.net"
    ]
  }
}
```

List all resource record sets in a hosted zone:

```
$ aws route53 list-resource-record-sets --hosted-zone-id 1234567890ABC
{
  "IsTruncated": false,
  "ResourceRecordSets": [
    {
      "ResourceRecords": [
        {
          "Value": "192.168.153.123"
        }
      ],
      "Type": "A",
      "Name": "realguess.net.",
      "TTL": 172800
    },
    {
      "ResourceRecords": [
        {
          "Value": "ns-1727.awsdns-23.co.uk."
        },
        {
          "Value": "ns-1312.awsdns-36.org."
        },
        {
          "Value": "ns-402.awsdns-50.com."
        },
        {
          "Value": "ns-587.awsdns-09.net."
        }
      ], 
      "Type": "NS",
      "Name": "realguess.net.",
      "TTL": 172800
    }, 
    {
      "ResourceRecords": [
        {
          "Value": "ns-1727.awsdns-23.co.uk. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400"
        }
      ], 
      "Type": "SOA",
      "Name": "realguess.net.",
      "TTL": 900
    },
    {
      "ResourceRecords": [
        {
          "Value": "192.168.153.123"
        }
      ],
      "Type": "A",
      "Name": "www.realguess.net.",
      "TTL": 86400
    }
  ],
  "MaxItems": "100"
}
```

Retrieve a single record set:

```
$ aws route53 list-resource-record-sets --hosted-zone-id 1234567890ABC \
  --start-record-name www.realguess.net --start-record-type A --max-items 1
{
  "IsTruncated": false,
  "ResourceRecordSets": [
    {
      "ResourceRecords": [
        {
          "Value": "192.168.153.123"
        }
      ],
      "Type": "A",
      "Name": "www.realguess.net.",
      "TTL": 86400
    }
  ],
  "MaxItems": "1"
}
```

In order to create a new record set, first create a JSON file to describe the new record:

```
{
  "Comment": "A new record set for the zone.",
  "Changes": [
    {
      "Action": "CREATE",
      "ResourceRecordSet": {
        "Name": "api.realguess.net.",
        "Type": "CNAME",
        "TTL": 60,
        "ResourceRecords": [
          {
            "Value": "www.realguess.net"
          }
        ]
      }
    }
  ]
}
```

Add a new record set (note that the JSON file must use `file://` starting path):

```
$ aws route53 change-resource-record-sets --hosted-zone-id 1234567890ABC \
  --change-batch file:///path/to/record.json
{
  "ChangeInfo": {
    "Status": "PENDING",
    "Comment": "A new record set for the zone.",
    "SubmittedAt": "2013-12-06T00:00:00.000Z",
    "Id": "/change/CHANGEID123"
  }
}
```

The status of adding the new record is currently _pending_. Poll the server to get the updated status:

```
$ aws route53 get-change --id CHANGEID123
{
  "ChangeInfo": {
    "Status": "INSYNC",
    "Comment": "A new record set for the zone.",
    "SubmittedAt": "2013-12-06T00:00:00.000Z",
    "Id": "/change/CHANGEID123"
  }
}
```

A new record has been created and has been propagated to all hosts.
