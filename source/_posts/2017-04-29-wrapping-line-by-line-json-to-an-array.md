title: Wrapping Line by Line JSON to an Array
date: 2017-04-29 12:00:00
categories: [Coding]
tags: [jq, json, slurp, stream, line, array]
---

Some utilities print out JSON data (object) line by line. Line by line JSON is not really a valid JSON format, need to wrap it in bracket as an array.

For example, here are a few lines of JSON objects:

```sh
$ for i in $(seq 3); do echo '{"id":'$i'}'; done
{"id":1}
{"id":2}
{"id":3}
```

To combine them into a single array, we can use [jq][]'s **-s, --slurp** option:

```sh
$ for i in $(seq 3); do echo '{"id":'$i'}'; done | jq -cM -s
[{"id":1},{"id":2},{"id":3}]
```

The option reads the entire input stream into a large array. This is good until the input stream is too large to process, because jq is not producing output while *slurping* up the input. If the input is line by line JSON, and all you want to do is wrapping it into an array (with or even without the trailing newline), we can simply do the following:

```sh
$ for i in $(seq 3); do echo '{"id":'$i'}'; done |\
  awk 'BEGIN { printf "["; getline; printf "%s", $0 } { printf ",%s", $0 } END { print "]" }'
[{"id":1},{"id":2},{"id":3}]
```

This is with the newline printed. And it will solve the problem of a very large input. Data will simply stream out as it comes it. Try it with a forever `while` loop.

There is another option in jq named **--stream**, and it seems to be doing the same. But option **--slurp** overrides **--stream**, and the option itself is already so complicated.

In conclusion, you can use `jq -s` to wrap line by line JSON into an array. If you want to be safe, just use the awk example above.


[jq]: https://stedolan.github.io/jq/
