title: Dirname Does Not Follow Symlink
date: 2014-09-20 11:22:33
tags: [dirname, symlink, symbolic-link, path, best-practice, node]
---

Node's global object [__dirname] returns the name of the directory that the currently executing script resides in. However, if a directory is symbolic linked or a symlink, it still return the original path.

For example, the following directory structure:

```
/tmp/dirname/
├── foo
│   └── app.js
└── lib
    ├── bar -> ../foo
    └── util.js

3 directories, 2 files
```

The `app.js` contains the following line:

```
console.log(__dirname);
```

Run the script:

```
$ cd /tmp/dirname/foo && node app.js
/tmp/dirname/foo
```

You get the directory the `app.js` script is residing it. But if you do the same from the symlinked directory:

```
$ cd /tmp/dirname/lib/bar && node app.js
/tmp/dirname/foo
```

Well, you get the same answer, even the current working directory is different:

```
$ cd /tmp/dirname/lib/bar && pwd
/tmp/dirname/lib/bar
```

The return of `__dirname` does not follow symbolic link.

So, have to be extra careful when requiring files that is symbolic linked. If you think you are in `lib/bar` directory and try to require the `util.js` script in `app.js`:

```
var path = require('path');
console.log(require('../util'));
console.log(require(path.join(__dirname, '..', 'util')));
```

Both statements will throw module not found error. Path join does not help either.

The best practice is to avoid symlinks and relative directory requiring. Instead, set up required as modules, and install them in `node_modules` directory, then they will become top-level modules.

[__dirname]: http://nodejs.org/api/globals.html#globals_dirname
