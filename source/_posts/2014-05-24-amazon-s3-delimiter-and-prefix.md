title: Amazon S3 Delimiter and Prefix
date: 2014-05-24 22:17:41
tags: [delimiter, prefix, directory, folder, s3, aws, amazon, listobjects, aws-sdk, javascript]
---

[Amazon S3] is an inexpensive online file storage service, and there is the [JavaScript SDK] to use. There are things puzzling me when using the SDK were:

1. How to use parameters `Delimiter` and `Prefix`?
2. What is the difference between `CommonPrefixes` and `Contents`?
3. How to create a folder/directory with JavaScript SDK?


[Amazon S3]: http://aws.amazon.com/s3/
[JavaScript SDK]: http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html


To retrieve objects in an Amazon S3 bucket, the operation is `listObjects`. The `listObjects` does not return the content of the object, but the key and meta data such as size and owner of the object.

To make a call to get a list of objects in a bucket:

```javascript
s3.listObjects(params, function (err, data) {
  // ...
});
```

Where the `params` can be configured with the following parameters:

- Bucket
- Delimiter
- EncodingType
- Marker
- MaxKeys
- Prefix

But what are `Delimiter` and `Prefix`? And how to use them?

Let's start by creating some objects in an Amazon S3 bucket similar to the following file structure. This can be easily done by using the AWS Console.

```
.
├── directory
│   ├── directory
│   │   └── file
│   └── file
└── file

2 directories, 3 files
```

In Amazon S3, the objects are:

```
directory/
directory/directory/
directory/directory/file
directory/file
file
```

One thing to keep in mind is that Amazon S3 is not a file system. There is not really the concept of file and directory/folder. From the console, it might look like there are 2 directories and 3 files. But they are all objects. And objects are listed alphabetically by their keys. 

To make it a little bit more clear, let's invoke the `listObjects` method. Since the operation has only `Bucket` parameter is required:

```javascript
params = {
  Bucket: 'example'
};
```

The response `data` contains in the callback function:

```
{ Contents: 
   [ { Key: 'directory/',
       LastModified: ...,
       ETag: '"d41d8cd98f00b204e9800998ecf8427e"',
       Size: 0,
       Owner: [Object],
       StorageClass: 'STANDARD' },
     { Key: 'directory/directory/',
       LastModified: ...,
       ETag: '"d41d8cd98f00b204e9800998ecf8427e"',
       Size: 0,
       Owner: [Object],
       StorageClass: 'STANDARD' },
     { Key: 'directory/directory/file',
       LastModified: ...,
       ETag: '"d41d8cd98f00b204e9800998ecf8427e"',
       Size: 0,
       Owner: [Object],
       StorageClass: 'STANDARD' },
     { Key: 'directory/file',
       LastModified: ...,
       ETag: '"d41d8cd98f00b204e9800998ecf8427e"',
       Size: 0,
       Owner: [Object],
       StorageClass: 'STANDARD' },
     { Key: 'file',
       LastModified: ...,
       ETag: '"d41d8cd98f00b204e9800998ecf8427e"',
       Size: 0,
       Owner: [Object],
       StorageClass: 'STANDARD' } ],
  CommonPrefixes: [],
  Name: 'example',
  Prefix: '',
  Marker: '',
  MaxKeys: 1000,
  IsTruncated: false }
```

If this is a file structure, you might expect:

```
directory/
file
```

But it is not, because a bucket does not work like a folder or a directory, where the immediate files inside the directory is shown.  The objects inside the bucket are laid out flat and alphabetically.

In UNIX, a directory is a file, but in Amazon S3, everything is an object, and can be identified by key.

So, how to make Amazon S3 behave more like a folder or a directory? Or how to just list the content of first level right inside the bucket?

In order to make it work like directory you have to use `Delimiter` and `Prefix`. `Delimiter` is what you use to group keys. It does have to be a _single character_, it can be a string of characters. And `Prefix` limits the response to keys that begin with the specified prefix.


## Delimiter

Let's start by adding the following delimiter:

```javascript
params = {
  Bucket: 'example',
  Delimiter: '/'
};
```

You will get something more like a listing of a directory:

```
{ Contents: 
   [ { Key: 'file' } ],
  CommonPrefixes: [ { Prefix: 'directory/' } ],
  Name: 'example',
  Prefix: '',
  MaxKeys: 1000,
  Delimiter: '/',
  IsTruncated: false }
```

There are a directory `directory/` and a file `file`. What happened was that the following objects except `file` are grouped by the delimiter `/`:

```
directory/
directory/directory/
directory/directory/file
directory/file
file
```

So, result in:

```
directory/
file
```

This feels more like a listing of a directory or folder. But if we change `Delimiter` to `i`, then, you get no `Contents` and just the prefixes:

```
{ Contents: [],
  CommonPrefixes: [ { Prefix: 'di' }, { Prefix: 'fi' } ],
  Name: 'example',
  Prefix: '',
  MaxKeys: 1000,
  Delimiter: 'i',
  IsTruncated: false }
```

All keys can be grouped into two prefixes: `di` and `fi`. Therefore, Amazon S3 is not a file system, but might act like one if using the right parameters.

As I have mentioned that `Delimiter` does not need to be a single character:

```
{ Contents: 
   [ { Key: 'directory/' },
     { Key: 'directory/file' },
     { Key: 'file' } ],
  CommonPrefixes: [ { Prefix: 'directory/directory' } ],
  Name: 'example',
  Prefix: '',
  MaxKeys: 1000,
  Delimiter: '/directory',
  IsTruncated: false }
```

If recall the bucket structure:

```
directory/
directory/directory/
directory/directory/file
directory/file
file
```

Both `directory/directory/` and `directory/directory/file` are grouped into a common prefix: `directory/directory`, due to the common grouping string `/directory`.

Let's try another one with `Delimiter: 'directory'`:

```
{ Contents: 
   [ { Key: 'file' } ],
  CommonPrefixes: [ { Prefix: 'directory' } ],
  Name: 'example',
  Prefix: '',
  MaxKeys: 1000,
  Delimiter: 'directory',
  IsTruncated: false }
```

Okay, one more. Let's try `ry/fi`:

```
{ Contents: 
   [ { Key: 'directory/' },
     { Key: 'directory/directory/' },
     { Key: 'file' } ],
  CommonPrefixes: 
   [ { Prefix: 'directory/directory/fi' },
     { Prefix: 'directory/fi' } ],
  Name: 'example,
  Prefix: '',
  MaxKeys: 1000,
  Delimiter: 'ry/fi',
  IsTruncated: false }
```

So, remember that `Delimiter` is just providing a grouping functionality for keys. If you want it to behave like a file system, use `Delimiter: '/'`.


## Prefix

`Prefix` is much easier to understand, it is a filter that limits keys to be prefixed by the one specified.

With the same structure:

```
directory/
directory/directory/
directory/directory/file
directory/file
file
```

Let's set the `Prefix` parameter to `directory`:

```
{ Contents: 
   [ { Key: 'directory/' },
     { Key: 'directory/directory/' },
     { Key: 'directory/directory/file' },
     { Key: 'directory/file' } ],
  CommonPrefixes: [],
  Name: 'example',
  Prefix: 'directory',
  MaxKeys: 1000,
  IsTruncated: false }
```

How about `directory/`:

```
{ Contents: 
   [ { Key: 'directory/' },
     { Key: 'directory/directory/' },
     { Key: 'directory/directory/file' },
     { Key: 'directory/file' } ],
  CommonPrefixes: [],
  Prefix: 'directory/' }
```

Both `directory` and `directory/` prefixes are the same.

If we try something slightly different, `Prefix: 'directory/d'`:

```
{ Contents: 
   [ { Key: 'directory/directory/' },
     { Key: 'directory/directory/file' } ],
  CommonPrefixes: [],
  Prefix: 'directory/d' }
```

Putting all together with both `Delimiter: 'directory'` and `Prefix: 'directory'`:

```
{ Contents: 
   [ { Key: 'directory/' },
     { Key: 'directory/file' } ],
  CommonPrefixes: [ { Prefix: 'directory/directory' } ],
  Prefix: 'directory',
  Delimiter: 'directory' }
```

First, list the keys prefixed by `directory`:
```
directory/
directory/directory/
directory/directory/file
directory/file
```

Group them by the delimiter `directory` **with prefix `directory`**:

```
directory/directory
```

The result `Contents` are:

```
directory/
directory/file
```

and `CommonPrefixes` are:

```
directory/directory
```

Maybe changing `Delimiter` to `i` could give a better perspective:

```
{ Contents: 
   [ { Key: 'directory/' } ],
  CommonPrefixes: [ { Prefix: 'directory/di' }, { Prefix: 'directory/fi' } ],
  Prefix: 'directory',
  Delimiter: 'i' }
```

as:

```
directory/               # key to show
directory/directory/     # group to 'directory/di'
directory/directory/file # group to 'directory/di'
directory/file           # Group to 'directory/fi'
file                     # ignored due to prefix
```

One advantage of using Amazon S3 over listing a directory is that you don't need to concern about nested directories, everything is being flattened. So, you can loop it through just by specifying the `Prefix` property.


## Directory/Folder

If you're using the Amazon AWS console to "Create Folder", you can create a directory/folder and upload a file to inside the directory/folder. In effect, you are actually creating two objects with the following keys:

```
directory/
directory/file
```

If you use the following command to upload a file, the directory is not created:

```
$ aws s3 cp file s3://example/directory/file
```

Because, Amazon S3 is not a file system, but a key/value store. If you use `listObjects` method, you will just see one object. That is the same reason that you cannot copy a local directory:

```
$ aws s3 cp directory s3://example/directory
upload failed: aws/ to s3://example/directory [Errno 21] Is a directory: u'/home/chao/tmp/directory/'
```

But we can use the JavaScript SDK to create a directory/folder:

```javascript
s3.putObject({ Bucket: 'example', Key: 'directory/' }, function (err, data) {
  if (err) { return console.error(err); }

  console.log(data);
});
```

Note that you must use `directory/` with trailing slash instead the one without. Otherwise, it is just a regular file not a directory.
