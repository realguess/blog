title: Character by Character Diff
date: 2013-05-29 15:10:10
tags: [vimdiff, vim, diff]
---

I have two very long JSON strings. In a glance, they are the same, but their
MD5 hashes are not. The `diff` tool is line based, but those two JSON strings
are single line without newline character. So, I really need a tool that can
`diff` character by character instead of line by line and with highlighting.
What I have found is `vimdiff`.

Must have `vim` installed not just `vi`:

    The program 'vimdiff' can be found in the following packages:
     * vim
     * vim-gnome
     * vim-tiny
     * vim-athena
     * vim-gtk
     * vim-nox
    Try: sudo apt-get install <selected package>

What I have found out by using `vimdiff` is that both JSON strings are the
same if converting to objects, but the ordering of properties are different,
which is very difficult to tell by eyeballing.

References:

-  <http://unix.stackexchange.com/questions/11128/diff-within-a-line>
