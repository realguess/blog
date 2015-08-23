title: 'cURL: Read from File or Stdin'
date: 2015-08-23 12:00:00
categories: [Linux]
tags: [curl, stdin, pipe]
---

Redirect or pipe the content of a file or the output of another command as the input of [cURL], for example, making a `POST` request with cURL.

This is done by prefixing the data parameter with `@`, the rest is the filename to read data from. With `-`, the data is coming from `stdin`:

```sh
$ seq 17000 1 17001 | \
  curl \
    --request POST \
    --include \
    --header 'Content-Type: application/json' \
    --data-binary @- \
    --no-buffer \
    https://example.com/data
```

Command `seq 17000 1 17001` returns:

```sh
17000
17001
```

The size of the above two numbers is 12 bytes (including two newlines). But when reading data via `@` with `--data` option, "carriage returns and newlines will be stripped out" \[[1]\]. If you check your server, you will see the content length is only 10 bytes.

To correct this problem, we will send the raw data using `--data-binary` option instead. This preserves `\n` and `\r` characters.

[cURL]: http://curl.haxx.se/
[1]: http://curl.haxx.se/docs/manpage.html
