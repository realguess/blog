title: Squeeze Repeats
date: 2019-02-28T12:00:00
categories: [Computing]
tags: [tr, cli]
---

One option that I have seldom used in `tr` command is `-s` or `--squeeze-repeats`:

> replace each sequence of a repeated character that is listed in the last specified SET, with a single occurrence of that character - TR(1)

The best way to explain it is to demonstrate by example:

```sh
$ echo hello hello | tr l 1
he11o he11o
```

Apply the `-s` option:

```sh
$ echo hello hello | tr -s l 1
he1o he1o
```

The number of repeated character `1` has been _squeezed_ into a single occurrence.

Therefore, the tr command has the following three operations:

- Translate (default, no options)
- Delete (`-d`)
- Squeeze (`-s`)

To drop repeated characters, the second set is not necessary. This is very useful to remove extra spaces:

```sh
$ echo 'hello,     world!' | tr -s '[:blank]'
hello, world!
```

In Vim, to squeeze the extra blank in a line:

```vim
:.! tr -s [:blank:]
```

Additional examples from [GNU Coreutils Manual][].

[GNU Coreutils Manual]: https://www.gnu.org/software/coreutils/manual/coreutils.html#Squeezing-and-deleting

Delete blank lines:

```sh
$ tr -s '\n'
```

Put all words on lines by themselves:

```sh
$ tr -cs '[:alnum:]' '\n'
```

Quick summary for above mentioned use cases:

- Remove extra spaces
- Delete blank lines
- Put all words on lines by themselves

Notes:

```plain
tr (GNU coreutils) 8.29
```
