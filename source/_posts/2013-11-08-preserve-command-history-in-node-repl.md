title: Preserve command history in Node REPL
date: 2013-11-08 15:59:36
tags: [node, repl, command-history, rlwrap, coffeescript]
---

Node [REPL][] (Read-Eval-Print-Loop) is a standalone JavaScript interpreter for Node. To invoke it, simply type:

    $ node

However, after exit and re-enter the REPL, all the previous commands are lost. This behaves very much different from what we expect from shell.

To maintain a persistent history, we can use [rlwrap][] as suggested in this [answer][1].

Install [rlwrap][]:

    $ sudo apt-get install rlwrap

Create an alias:

    alias node='env NODE_NO_READLINE=1 rlwrap -s 1000 -S "node> " node'

Persistent history file is saved in:

    ~/.node_history

However, if by perserving the command history, Node REPL loses familiarity of color output and tab completion. This is a [trade-off][2]. To get around this problem, we can roll our own REPL or use an alternative. The best alternative I have found so far is also the one I am already using: [CoffeeScript][] (see 1.6.3 change log). There is a history command built-in:

    $ coffee
    coffee> .help
    .break  Sometimes you get stuck, this gets you out
    .clear  Break, and also clear the local context
    .exit   Exit the repl
    .help   Show repl options
    .history        Show command history
    .load   Load JS from a file into the REPL session
    .save   Save all evaluated commands in this REPL session to a file

[repl]: http://nodejs.org/api/repl.html
[rlwrap]: http://utopia.knoware.nl/~hlub/uck/rlwrap/rlwrap.html
[coffeescript]: http://coffeescript.org/#changelog
[1]: http://stackoverflow.com/a/18513951
[2]: http://stackoverflow.com/a/9219349
