title: Randomizing an Array with Sort
date: 2016-09-12 12:00:00
categories: [Computing]
tags: [random, array, sort]
---

How to randomize an array? Use the `sort` command, with the option:

```
-R, --random-sort
    sort by random hash of keys
```

For example:

```
$ seq 1 10 | sort -R
4
2
10
6
3
9
7
5
8
1
$ seq 1 10 | sort --random-sort
9
6
1
3
2
8
7
5
4
10
```
