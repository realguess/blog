title: Scaffold an Explicit Document to Avoid Error When Creating Indexes in Google Cloud Datastore
date: 2015-09-15 12:00:00
categories: [Cloud]
tags: [index, google-cloud-datastore, explicit-document, directives-end-marker, yaml, create-indexes, gcloud]
---

When working with [Google Cloud Datastore], I would like to design each [kind] (collection or table) with its own schema and index files:

```sh
$ tree
.
├── index.yml
└── schema.yml
```

But not every kind has to have something in the `index.yml` file. So, what to do? Will the index creation command accept an empty `index.yml` file with zero byte? Let's give try:

```sh
$ gcloud preview datastore create-indexes index.yml

ERROR: (gcloud.preview.datastore.create-indexes) An error occurred while parsing file: [/home/chao/kind/index.yml]
The file is empty
```

No, it does not like it. Then, what's the minimum required? The answer is an [explicit document] with an empty document content:

```yml
---
```

The three dashes form a [directives end marker] line. [YAML] uses three dashes to separate directives from document content.

When creating indexes with the file, it will proceed without errors or warnings:

```sh
$ gcloud preview datastore create-indexes index.yml

You are about to update the following configurations:
- myproj/index  From: [/home/chao/kind/index.yml]
Do you want to continue (Y/n)?
```

Therefore, to avoid error, when scaffold an `index.yml` file for indexing, use an explicit document with an empty document content.

[YAML]: http://www.yaml.org/
[Google Cloud Datastore]: https://cloud.google.com/datastore/
[kind]: https://cloud.google.com/datastore/docs/concepts/entities#Datastore_Kinds_and_identifiers
[explicit document]: http://www.yaml.org/spec/1.2/spec.html#id2801301
[directives end marker]: http://www.yaml.org/spec/1.2/spec.html#marker/directives%20end/
