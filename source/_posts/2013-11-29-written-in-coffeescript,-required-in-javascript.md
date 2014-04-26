title: Written in CoffeeScript, Required in JavaScript
date: 2013-11-29 12:00:30
tags: [best-practice, coffeescript, javascript, npm, preinstall, npm-module]
---

If you are writing in CoffeeScript, then requiring another module written in CoffeeScript works the same as as both scripts are in JavaScript:

```coffeescript
cm = require 'coffee-module'
```

But if you are writing in JavaScript, and the dependent module is in CoffeeScript, you have to include CoffeeScript as a dependency with require statement:

```javascript
require('coffee-script');
var cm = require('coffee-module');
```

For better compatibility, source codes written in CoffeeScript should be compiled into JavaScript. But what is the best practice? You don't want to maintain two languages. When to compile? Here is two options:

1. Compile before publish the module
2. Compile after module install

The advantage of the first approach is that there is no dependency on CoffeeScript. The module has been compiled into JavaScript prior to submitting to module registry. However, this approach requires two repositories, one is source code repository, and another one is the published module repository. If you are working on a private project, it is less likely that you will publish your module to an public NPM registry or running your own private one. It is more likely to have a single source code repository. Therefore, the second approach might be better in this situation. However, `coffee-script` must be added in `dependency`, or it must be installed globally with `coffee` command available during `preinstall` phase. Although this approach is not recommended in [npm-scripts][], before setting up a private NPM registry, this is the way to go.

Here is the required fields in `package.json`:
```json
{
  "scripts": {
    "preinstall": "coffee --compile --bare --output lib/ src/"
  }
}
```

[npm-scripts]: https://npmjs.org/doc/misc/npm-scripts.html
