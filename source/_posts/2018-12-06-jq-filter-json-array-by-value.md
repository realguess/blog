title: 'jq: Filter JSON Array by Value'
date: 2018-12-05T12:00:00
categories: [Coding]
tags: [jq, json, array, filter]
---

Print the JSON object in an array where the object key is equal to the specified value with [jq]:

```sh
$ echo '[{"key":"foo"},{"key":"bar"}]' | jq '.[] | select(.key == "foo")'
{
  "key": "foo"
}
```

Reference:

```sh
$ jq --version
jq-1.5
```

[jq]: https://stedolan.github.io/jq/
