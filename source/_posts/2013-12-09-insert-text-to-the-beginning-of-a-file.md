title: Insert Text to the Beginning of a File
date: 2013-12-09 18:09:08
tags: [echo, cat, head]
---

It is easy to append some text to the end of another file:

    $ cat foo >> bar

or even just portion of a file:

    $ head -n 2 foo >> bar

but how about to the beginning of a file? Well, it is not that bad to do either:

    $ echo "$(cat foo bar)" > bar

just a portion of a file:

    $ echo "$(head -n 2 foo)\n$(cat bar)" > bar

Now you can easily add some text such as copyright information to the beginning of another file with one command.
