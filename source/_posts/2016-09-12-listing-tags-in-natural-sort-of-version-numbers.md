title: Listing Tags in Natural Sort of Version Numbers
date: 2016-09-12 12:00:00
categories: [Coding]
tags: [natural-sort, version-sort, sort, version, git-tag, git]
---

Using the Node.js repository as an example:

```
$ git remote -v
origin  https://github.com/nodejs/node.git (fetch)
origin  https://github.com/nodejs/node.git (push)
```

If we would like to list all tags with `v0.12` versions, we could do:

```
$ git tag -l 'v0.12.*'
v0.12.0
v0.12.1
v0.12.10
v0.12.11
v0.12.12
v0.12.13
v0.12.14
v0.12.15
v0.12.2
v0.12.3
v0.12.4
v0.12.5
v0.12.6
v0.12.7
v0.12.8
v0.12.8-rc.1
v0.12.9
```

However, `v0.12.2` should come after `v0.12.1`.

To fix it, we use the `sort` command with option:

```
-V, --version-sort
    natural sort of (version) numbers within text
```

Thus:

```
$ git tag -l 'v0.12.*' | sort --version-sort
v0.12.0
v0.12.1
v0.12.2
v0.12.3
v0.12.4
v0.12.5
v0.12.6
v0.12.7
v0.12.8
v0.12.8-rc.1
v0.12.9
v0.12.10
v0.12.11
v0.12.12
v0.12.13
v0.12.14
v0.12.15
```
