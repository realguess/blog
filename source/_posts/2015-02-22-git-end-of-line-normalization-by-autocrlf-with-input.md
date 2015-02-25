title: Git End-of-Line Normalization by AutoCRLF with Input
date: 2015-02-22 12:00:00
categories: [Tools]
tags: [eol, line-ending, crlf, lf, git, vagrant, windows]
---

Using Vagrant file provision in a Windows host, the line endings are not converted from CRLF to LF (Linux systems). So, code cannot be executed properly in the guest Linux system. And also this is a frequently occurred message for working with Windows:

```plain
warning: LF will be replaced by CRLF in README.md.
The file will have its original line endings in your working directory.
```

One solution is to use UNIX line ending (LF) even in Windows, hence no conversion is needed. This is done by setting:

```sh
$ git config --global core.autocrlf input
```

Which means when committing the code to a remote repository, the line ending will be convert from CRLF to LF if any, but when checking out from the remote directory, no conversion from LF to CRLF is performed.

<!-- more -->

Also, use `.gitattributes` file per repository to ensure consistent behavior for all users.

```
* text=auto
* text eol=lf
```

This ensures that all text files will have line endings set to LF in the repository and your working directory.

See `man gitattributes`, and `git help config`, and [Deal with line endings][1].

[1]: https://help.github.com/articles/dealing-with-line-endings/
