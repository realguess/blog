title: Understand the Limitation of the String Property Type of Google Cloud Datastore
date: 2015-09-14 12:00:00
categories: [Cloud]
tags: [string, buffer, google-cloud-datastore, datastore, database, db, validation, lorem-ipsum]
---

[Google Cloud Datastore] supports a variety of data types for property values:

 - Integers
 - Floating-point numbers
 - Strings
 - Dates
 - Binary data

Strings are likely the most frequently used. When working with strings, the most important question to ask is how many bytes can be inserted into the property/field. According to the table listed in [properties and value types][1]:

> Up to 1500 bytes if property is indexed, up to 1MB otherwise

Let's give a try:

```sh
// String Property Type
// ====================
//
// Investigate the string property type of Google Cloud Datastore.
//
// Dependencies tested:
//
// - `gcloud@v0.21.0`
// - `lorem-ipsum@1.0.3`

'use strict';

// Define dependencies.
var params = {
  projectId: 'myproj',
  keyFilename: '/path/to/key/file.json',
  namespace: 'test'
};
var dataset = require('gcloud').datastore.dataset(params);
var kind = 'String';
var lorem = require('lorem-ipsum');

// Save an entity to the datastore.
function run(params) {
  var size = params.size;
  var index = params.index;
  var str = lorem({ count: size }).substr(0, size);
  var key = dataset.key(kind);
  var data = [
    {
      name: 'title',
      value: str,
      excludeFromIndexes: !index
    }
  ];
  var entity = {
    key: key,
    data: data
  };

  dataset.save(entity, function (err) {
    console.log(size, new Buffer(str).length, index,
      err ? err.code + ' ' + err.message : '200 OK');
  });
}

// Explanation of fields:
//
// - `size`: Total number of bytes to produce
// - `index`: Whether to index the string field
//
// In-line comment indicates the expected result.
[
  { size: 1500, index: true  },             // OK
  { size: 1501, index: false },             // OK
  { size: 1501, index: true  },             // Error
  { size: 1024 * 1024 - 92, index: false }, // OK
  { size: 1024 * 1024 - 91, index: false }, // Error
].forEach(run);
```

Don't ask me where `91` or `92` is coming from. Apparently, this is somewhere closer to 1 MB, but not exactly. The result of the test script:

<!-- more -->

```sh
1500 1500 true '200 OK'
1501 1501 false '200 OK'
1501 1501 true '400 Bad Request'
1048484 1048484 false '200 OK'
1048485 1048485 false '400 Bad Request'
```

As expected, if the string field is indexed, it can only take up to 1500 bytes. But it if not indexed, it can take up to 1,048,484 bytes, somewhere closer to 1 MB.

By knowing this, before making a request to Google Cloud Datastore, we can optimize our code by validating the size ahead, hence one less network request. For example, disallow saving unindexed string value exceeding the limit:

```js
// Convert string into a buffer.
var size = new Buffer(body).length;

// Request validation: more than 1 MB.
if (size > 1024 * 1024 - 91) {
  throw new Error('Body size is larger than the ~1 MB limit: ' + size);
}
```

Anything large than 1MB should be better stored in [Google Cloud Storage].

But when working with JSON based API, it's better to limit the actual size to much less, like 1 KB. Because when getting a list of posts, usually 20 posts are returned. If allowing 1 MB for each post, then there will be more than 20 MB for each query. Therefore, it's better to limit the string field to a much smaller value. Even you can do it, it does not mean you have to do it.

[Google Cloud Datastore]: https://cloud.google.com/datastore/
[Google Cloud Storage]: https://cloud.google.com/storage/
[1]: https://cloud.google.com/datastore/docs/concepts/entities#Datastore_Properties_and_value_types
