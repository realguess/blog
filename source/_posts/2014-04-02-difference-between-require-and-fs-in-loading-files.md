title: Difference between require and fs in Loading Files
date: 2014-04-02 00:30:00
tags: [path, relative-path, absolute-path, normalization, require, file-system, fs, join, node]
---

Node has a simple module loading system by using `require`.

> A module prefixed with `'/'` is an absolute path to the file. For example, `require('/home/marco/foo.js')` will load the file at `/home/marco/foo.js`.
>
> A module prefixed with `'./'` is relative to the file calling `require()`. That is, `circle.js` must be in the same directory as `foo.js` for `require('./circle')` to find it.
>
> Without a leading `'/'` or `'./'` to indicate a file, the module is either a "core module" or is loaded from a `node_modules` folder.
>
> <http://nodejs.org/api/modules.html#modules_file_modules>

We can also use it to load JSON files by simply specifying the file path. File path can be relative or absolute. Giving the following file structure:

```
.
├── app.js
├── data
│   └── file.json
└── lib
    └── util.js
```

Let's say the root path of the application is `/home/marco`. Use relative path from the file `lib/util.js`:
```javascript
var json = require('../data/file.json');
```

This will load the JSON content from `data/file.json`, because the require system knows that the file is relative to the calling file `lib/util.js`. But when using [file system] functions, relative path is not treat the same way:

> Relative path to filename can be used, remember however that this path will be relative to `process.cwd()`. - [File System]

`process.cwd()` returns the current working directory, not the root directory of the application nor the directory of the calling file. For example:
```javascript
var data = fs.readFileSync('../data/file.json');
```

If your current working directory is `/home/marco/myapp`, the application root directory, you will get the following error message:
```
fs.js:410
  return binding.open(pathModule._makeLong(path), stringToFlags(flags), mode);
                 ^
Error: ENOENT, no such file or directory '../data/file.json'
    at Object.fs.openSync (fs.js:410:18)
    at Object.fs.readFileSync (fs.js:276:15)
```

Because `process.cwd()` is `/home/marco/myapp`, and reading file relative to this directory with `../data/file.json`, Node will expect file path to be `/home/marco/data/file.json`. Therefore, the error is thrown.

The difference between `require` and `fs` in loading files is that if the relative path is used in `require`, it is relative to the file calling the `require`. If the relative path is used in `fs`, it is relative to `process.cwd()`.

To fix the error in file system functions, we should always use absolute path. One thing we can do is:
```javascript
var data = fs.readFileSync(__dirname + '/../data/file.json');
```

`__dirname` is "the name of the directory that the currently executing script is resides in" [[dirname]], and it has no trailing slash. The the structure looks odd, even though it works, but it might not work in all systems.

The correct solution here is to use `path.join` from [Path]:
```javascript
var data = fs.readFileSync(path.join(__dirname, '../data/file.json'));
```

The `path` module "contains utilities for handling and transforming file paths". The `path.join` function will call `path.normalize` to "normalize a string path, taking care of `..` and `.` paths" [[normalize]], also see function `normalizeArray` in <https://github.com/joyent/node/blob/master/lib/path.js> on how the paths are normalized.

In conclusion:
```javascript
// Use `require` to load JSON file.
var json = require('../data/file.json');

// Use `fs` to load JSON file.
var data = fs.readFileSync(path.join(__dirname, '../data/file.json'));
var json = JSON.parse(data.toString());
```

[file system]: http://nodejs.org/api/fs.html
[dirname]: http://nodejs.org/api/globals.html#globals_dirname
[path]: http://nodejs.org/api/path.html
[normalize]: http://nodejs.org/api/path.html#path_path_normalize_p
