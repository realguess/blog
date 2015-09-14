title: 'Google Cloud Datastore Starter: Dataset'
date: 2015-09-14 12:00:00
categories: [Cloud]
tags: [starter, google-cloud-datastore, dataset]
---

[Google Cloud Datastore] dataset starter, part of a starter collection:

```js
// Google Cloud Datastore Starter: Dataset
// =======================================

'use strict';

// Define a dataset.
var params = {
  projectId: 'MY_PROJECT',
  keyFilename: '/path/to/key/file.json',
  namespace: 'MY_NAMESPACE'
};
var dataset = require('gcloud').datastore.dataset(params);

// Define an entity (including both key and data).
var kind = 'MY_KIND';
var key = dataset.key(kind);
var data = [
  {
    name: 'title',
    value: 'Google Cloud Datastore Starter',
    excludeFromIndexes: false
  }
];
// var data = { title: 'Google Cloud Datastore Starter' }; // Simple version
var entity = {
  key: key,
  data: data
};

// Save a single entity.
dataset.save(entity, function (err) {
  if (err) {
    throw err;
  }
});
```

[Google Cloud Datastore]: https://cloud.google.com/datastore/
