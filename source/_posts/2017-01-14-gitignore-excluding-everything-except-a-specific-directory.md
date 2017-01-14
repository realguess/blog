title: 'Gitignore: Excluding Everything Except a Specific Directory'
date: 2017-01-14 12:00:00
categories: [Coding]
tags: [exclude, directory, gitignore, git]
---

I have the following directory structure:

```
$ tree -aF src/
src/
├── github.com/
│   ├── git/
│   │   └── git/
│   │       └── file
│   └── realguess/
│       ├── git/
│       │   └── file
│       └── hello/
│           └── hello.go
└── .gitignore

6 directories, 4 files
```

My intention is to keep the projects in `src/github.com/realguess/` only. So, I have added the following gitignore file:

```
$ cat src/.gitignore
/github.com/
!/github.com/realguess/
```

But the directory is ignored:

```
$ git check-ignore -v github.com/realguess/
src/.gitignore:1:/github.com/      github.com/realguess/
```

> **It is not possible to re-include a file if a parent directory of that file is excluded.** Git doesn't list excluded directories for performance reasons, so any patterns on contained files have no effect, no matter where they are defined.[^1]

Uh-oh! The directory `github.com/` is excluded. Therefore, it is *not possible to re-include a file* `github.com/realguess` (directory is a file). To fix it, just need to add the wildcard to the end of the parent directory:

```
$ cat src/.gitignore
/github.com/*
!/github.com/realguess/
```

By adding the wildcard character, it matches every file (including hidden ones) or directory under `github.com/` directory, the directory itself as the parent of `github.com/realguess/` is not excluded. Then, we use the negation pattern to re-include `github.com/realguess/`.

Double check it:

```
$ git check-ignore -v github.com/realguess/
1
```

The debugging command returns non-zero status code, which indicates that the directory is not ignored.

<!-- more -->

We can localize the problem by moving `.gitignore` file into `github.com/` directory:

```
$ tree -aF src/
src/
└── github.com/
    ├── git/
    │   └── git/
    │       └── file
    ├── .gitignore
    └── realguess/
        ├── git/
        │   └── file
        └── hello/
            └── hello.go

6 directories, 4 files
```

The ignore file contains:

```
$ cat src/github.com/.gitignore
/*
!/.gitignore
!/realguess/
```

Less typing on `github.com`. The only caveat is to add back `.gitignore` file.

The general pattern to *exclude everything but a specific directory*:

1. Localize the gitigore (*personal preference*)
2. Use the wildcard
3. Negate

```
$ cat exclude.me/.gitignore
/*
!/.gitigore
!/except.me/
```


[^1]:[Pattern Format](https://git-scm.com/docs/gitignore#_pattern_format), gitignore
