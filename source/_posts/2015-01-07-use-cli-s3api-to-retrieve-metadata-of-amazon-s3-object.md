title: Use CLI s3api to Retrieve Metadata of Amazon S3 Object
date: 2015-01-07 12:00:00
categories: [Cloud]
tags: [amazon-s3api, amazon-s3, aws-cli, aws, command-line, cli, amazon]
---

In [Amazon S3 CLI], there are only a handful commands:

```plain
cp      ls      mb      mv      rb      rm      sync    website
```

We can use `cp` command to retrieve S3 object:

```sh
$ aws s3 cp s3://mybucket/myfile.json myfile.json
```

But if only the metadata of the object, such as `ETag` or `Content-Type` is needed, the S3 CLI does not have any command to do that.

Now enter [S3API CLI]. Not only the CLI commands can retrieve S3 objects, but also associated [metadata]. For example, retrieve S3 object similar to `aws s3 cp`:

```sh
$ aws s3api get-object --bucket mybucket --key myfile.json
```

Just need the metadata of the object, use `head-object`, which retrieves metadata without the object itself, as HTTP HEAD method:

<!-- more -->

```sh
$ aws s3api head-object --bucket mybucket --key myfile.json
{
  "AcceptRanges": "bytes", 
  "ContentType": "application/json", 
  "LastModified": "Wed, 07 Jan 2015 12:00:00 GMT", 
  "ContentLength": 2015, 
  "ETag": "\"63aa0f9df87f6b54f150cf8a1011d2b0\"", 
  "Metadata": {}
}
```


[Amazon S3 CLI]: http://docs.aws.amazon.com/cli/latest/reference/s3/index.html#available-commands
[S3API CLI]: http://docs.aws.amazon.com/cli/latest/reference/s3api/index.html
[metadata]: http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-metadata
