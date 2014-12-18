title: Update Outdated NPM Packages
date: 2014-12-13 12:00:00
categories: [Tools]
tags: [outdated, update, npm, package, dependency, color, semver]
---

[NPM] provides a command to check for outdated packages:

```sh
$ npm help outdated
```

However, by default, the command checks the entire dependency tree, not only the modules specified in `package.json`, but also the dependencies of those modules. If we only care about the top-level packages, we can add `--depth` option to show just that:

```sh
$ npm outdated --depth 0
```

This is similar to listing installed packages:

```sh
$ npm list --depth 0
```

Using the option, it will not print out the nested dependency tree, but only the top-level. The option is similar to `tree -L 1`, but zero indexed instead of one.

Another interesting thing about `outdated` command is the color coding in the output:

<!-- more -->

![Colors of `npm outdated` output](npm-outdated.png)

Do not want colors? We can turn if off:

```sh
$ npm outdated --depth 0 --quiet --color false
```

And here are the corresponding modules specified in `package.json`:

```
"dependencies": {
  "koa": "^0.8.2",
  "koa-router": "^3.2.1",
  ...
}
```

Due to [SemVer] `^x.y.z` [caret format], _red_ colors clearly indicate that packages are out of date, and they need to be upgraded. And _yellow_ ones indicate that packages are out of date as well, but to upgrade, there might be breaking changes. (Since `koa` is still `0.x.x`, so the _caret_ rule applies to minor.)

All packages in red should be upgraded first, then upgrade ones in yellow, because of the possibility of breaking change.

How to update all packages with a single command? This is actually pretty easy:

```sh
$ npm update
```

Be careful, not all packages are updated, but only the ones in _red_. Since the ones in red are either patch or minor releases, no breaking change, so they are safe to be upgraded.

It is not really necessary to have the updated versions to be written into `package.json` file. As long as the SemVer rules are followed, the latest packages will be installed.

Now, if you examine the yellow ones and feel good on the new major upgrade, then remove the old package, and install the new one.

```sh
$ npm update --save koa@0.9.0
```

This command does not work. But `install` command works:

```sh
$ npm install --save koa@0.9.0
```

To install the latest version and also update `package.json`:

```sh
$ npm install --save koa@latest
```

In summary, here is the the workflow to keep dependencies up to date:

1. Issue `npm outdated --depth 0` to show outdated packages.
2. Run `npm update` to update all packages that have no breaking change.
3. Examine packages that might have breaking change, and upgrade them one by one.


[NPM]: https://www.npmjs.com/
[Semver]: http://semver.org/
[caret format]: https://github.com/npm/node-semver#caret-ranges-123-025-004
