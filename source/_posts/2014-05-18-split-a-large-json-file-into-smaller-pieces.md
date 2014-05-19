title: Split a Large JSON file into Smaller Pieces
date: 2014-05-18 22:05:34
tags: [json, jq, split, stream, sed, echo]
---

In the [previous post], I have written about how to split a large JSON file into multiple parts, but that was limited to the default behavior of `mongoexport`, where each line in the output file represents a JSON string. If you have to deal with a large JSON file, such as the one generated with `--jsonArray` option in `mongoexport`, you can to parse the file incrementally or streaming.

I have downloaded a large JOSN [data set] (about 144MB) from [Data.gov]. If you try to read the entire data set into memory:

    > var json = require('./data.json')
    Killed

The process is not able to handle it. Use streaming is necessary. And luckily, our [command line JSON processing] tool, [jq], supports streaming.

The parts we are interested are encapsulated in an array under `data` property of the data set. We are going to split each element of the array into its own file.

Don't try to use `-f` option in _jq_ to read file from the command line, it will read everything into a memory. Instead, do `cat data.json | jq`.

    $ mkdir parts
    $ cat data.json | jq -c -M '.data[]' | \
      while read line; do echo $line > parts/$(date +%s%N).json; done

The entire data set is piped into _jq_ to filter and compress each array element. Each element is printed in one line, and each line is saved into its own JSON file by using UNIX timestamp plus nanosecond as the filename. All pieces are saved into `parts/` directory.

But there is one problem with embedded JSON string, which has to do with `echo`, due to backslash. For example, if echoing the following string:

    {"name":"{\"first\":\"Foo\",\"last\":\"Foo\"}","username":"foo","id":1}

It will be printed as an invalid JSON:

    {"name":"{"first":"Foo","last":"Foo"}","username":"foo","id":1}
    
Backslashes are stripped. To fix this problem, we can simply double backslash:

    $ cat data.json | jq -c -M '.data[]' | sed 's/\\"/\\\\"/g' | \
      while read line; do echo $line > parts/$(date +%s%N).json; done

You can even try `curl` the remote JSON file instead using `cat` from the downloaded file. But you might want to try with a smaller file first, because, with my slow machine, it took me nearly an hour to finish splitting into 678,733 parts:

    real    49m35.780s
    user    2m42.888s
    sys     6m48.048s

To take it a little bit further, the next step is to decide how many lines or array elements to write into a single file.


[previous post]: /2013/12/19/split-json-file-into-multiple-parts/
[data set]: https://health.data.ny.gov/api/views/vafa-pf2s/rows.json?accessType=DOWNLOAD
[Data.gov]: http://catalog.data.gov/dataset/genealogical-research-death-index-beginning-1957
[command line JSON processing]: /2014/05/18/command-line-json-processing/
[jq]: http://stedolan.github.io/jq/
