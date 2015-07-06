title: "Fields in 'package.json' for Private and Unlicensed Modules"
date: 2015-07-06 12:00:00
categories: [Node.js and io.js]
tags: [license, package.json, npm]
---

If you not include the `license` field in `package.json`, [npm] will keep bugging you:

```sh
INFO: package.json
INFO: api@0.1.8 No license field.
```

If your module is private, just add the following two fields to `package.json`:

```json
{
  "license": "UNLICENSED",
  "private": true
}
```

See NPM `package.json` [license].

[npm]: https://www.npmjs.com/
[license]: https://docs.npmjs.com/files/package.json#license