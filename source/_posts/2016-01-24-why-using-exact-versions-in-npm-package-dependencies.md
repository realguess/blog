title: Why Using Exact Versions in NPM Package Dependencies
date: 2016-01-24 12:00:00
categories: [JavaScript]
tags: [caret, caret-range, version, package.json, dependency, npm, js-yaml, duplicate-keys, node]
---

Versions frequently used in [NPM package dependencies] are [caret ranges] or `^version`. And `npm install` uses caret ranges as well.

Caret range:

> Allows changes that do not modify the left-most non-zero digit in the **[major, minor, patch]** tuple. In other words, this allows patch and minor updates for versions **1.0.0** and above, patch updates for version **0.x >=0.1.0**, and *no* updates for version **0.0.x**.

Most of time, using caret ranges as dependency versions work perfectly fine. But once a while, there are bugs. In one of projects, [JS-YAML] with caret version `^3.4.3` was used and installed:

```json
{
  "dependencies": {
    "js-yaml": "^3.4.3"
  }
}
```

Time passes. When `npm install` was run again from a freshly cloned project code base, version `3.5.2` was installed. Since JS-YAML version `3.5.0`, errors are thrown when safe loading a spec with duplicate keys. If there are no duplicate keys in the YAML file, this will be fine. However, one of the files has it. The correct approach is to actually fix the duplicate keys. But it requires additional work back and forth. You've heard this before: "Hey it works when I installed it."

The point here is to use **exact versions** instead of letting the package manager decides by dropping the caret character:

```json
{
  "dependencies": {
    "js-yaml": "3.4.3"
  }
}
```

This will avoid the above mentioned problem. We can manually [update the outdated NPM packages][2014-12-13].

Don't let machine decide. You Do!


[NPM package dependencies]: https://docs.npmjs.com/files/package.json#dependencies
[Caret ranges]: https://docs.npmjs.com/misc/semver#caret-ranges-1-2-3-0-2-5-0-0-4
[JS-YAML]: https://github.com/nodeca/js-yaml
[2014-12-13]: https://realguess.net/2014/12/13/update-outdated-npm-packages/
