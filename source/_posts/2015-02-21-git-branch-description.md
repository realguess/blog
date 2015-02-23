title: Git Branch Description
date: 2015-02-21 12:00:00
categories: [Tools]
tags: [git-branch, git]
---

Branch is cheap, use it often. But when you create too many, knowing which is for what becomes a management problem. Here are two solutions:

1. Use `--edit-description` to provide more than just a simple branch name
2. Delete all merged branches

For the first solution:

```sh
$ git branch --edit-description
$ git config branch.master.description
```

Also see:

- [Taking Advantage of Git Branch Descriptions](http://labs.sixdimensions.com/blog/2015-02-19/taking-advantage-git-branch-descriptions/)
- [Git branches with descriptions, really](http://bahmutov.calepin.co/git-branches-with-descriptions-really.html)

I prefer the second approach.
