title: Split JSON File into Multiple Parts
date: 2013-12-19 16:56:13
tags: [split, json, multi-parts, mongodb, mongoexport, awk]
---

`mongoexport` allows us to export documents from MongoDB into a JSON file:

    $ mongoexport -d mydb -c mycollection -o myfile.json --jsonArray

The `--jsonArray` option writes the entire content of the export as a single JSON array. Sometimes, the size of individual file is quite large, we need to break it down into small pieces, because most web servers will have a limit on how much data can be submitted at once. However, with the option, the entire document is a single line. Any line processing command cannot be easily used. (If you are looking to do so, you can use `jq` to [split a large JSON file into smaller pieces].) We can omit the option (default behavior) and have the export utility to dump it one document at a time. The entire exported JSON file is technically not in correct JSON format, but each line, which represents a MongoDB document, is valid JSON, and can be used to do some command line processing.

To break a large file into many smaller pieces, we can use `split` command:

    $ split -l 10 data.json

The `-l` or `--lines` option limits each file with a maximum of 10 lines.

Another way we can use `-C` or `--line-bytes` option to put at most 1k bytes of lines per output file:

    $ split -d -a 3 -C 1k data.json

One thing needs to make sure is that the size of each line is no more than the maximum size specified by the option, otherwise, partial lines will be generated.

It is good to keep all those parts in their own directory:

    $ mkdir pieces && cd pieces && split -d -a 3 -C 1k ../data.json && cd ..

Unless breaking the file into one line at a time, otherwise, we need to convert individual JSON file into correct JSON format:

    $ find pieces/* -exec sh -c \
    > "awk 'BEGIN{l=\"[\"}{print l;l=\$0\",\"}END{print\$0\"\n]\"}' \
    > {} > {}.json && rm {}" \;

The output JSON file will contains an array of MongoDB documents. The main idea of the AWK script is to print out previous line as it reads the current line. `BEGIN { l = "[" }` defines the first line as the opening square bracket, and the `END { print $0"\n" }` prints the last line of the file and the closing square bracket.

There bounds to be a better way. Just need to keep looking.

[split a large JSON file into smaller pieces]: /2014/05/18/split-a-large-json-file-into-smaller-pieces/
