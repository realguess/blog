title: Set Filename Path with process.cwd()
date: 2013-12-18 15:12:43
tags: [filename, path, process, cwd, node]
---

When working with [file system][fs] in Node, you will need a fully qualified filename to do things like reading the content of a file:

    require('fs').readFile(filename, function (err, data) {});

If you have the following directory structure:

    .
    ├── app.js
    ├── data
    │   └── file.txt
    └── lib
        └── reader.js

There are two ways to read the content of `data/file.txt` from `lib/reader.js`:

Use the current directory of the script `__dirname`:

    filename = __dirname + '/../data/file.txt';

or with `path.join` to normalize the resulting path:

    filename = path.join(__dirname, '../data/file.txt');

The second method is to use the current working directory of the process `process.cwd()`:

    filename = process.cwd() + '/data/file.txt';

The second approach is more portable. If you move `lib/reader.js` to `lib/utils/reader.js`, no code change will be needed. But make sure the directory of the Node process is the application root directory where `node app.js` is being issued.

[fs]: http://nodejs.org/api/fs.html
