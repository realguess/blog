title: Node Version Manager
date: 2014-05-20 13:49:37
tags: [nvm, node-version-manager, version, node, bash]
---

[Node Version Manager][nvm] is a simple bash script to manage multiple active [Node] versions. _Why do you want to use it?_ If you like me, you want to try out the latest unstable version of the Node, but you still need to use the older and stable versions to develop and maintain your projects, then you should use [NVM].

You can find out more information on [NVM GitHub page][nvm], below are just my take on installing and using it.


## Install

You can install NVM via the install script, but always do it the hard way by installing it manually:

    $ git clone https://github.com/creationix/nvm.git ~/.nvm

Check out the latest version:

    $ cd ~/.nvm && git checkout v0.7.0

Enable NVM:

    $ source ~/.nvm/nvm.sh

Add this to `~/.bashrc` to make it available upon login:

    $ echo "\n# Enable NVM"           >> ~/.bashrc
    $ echo 'source $HOME/.nvm/nvm.sh' >> ~/.bashrc

Enable Bash completion:

    $ echo 'source $HOME/.nvm/bash_completion' >> ~/.bashrc

Try it by opening a new terminal:

    $ nvm


## Usage

Get help:

    $ nvm help

Show current NVM version:

    $ nvm --version

Display the currently active Node version:

    $ nvm current

Install Node v0.10.28:

    $ nvm install 0.10.28

The installed Node version will reside in `~/.nvm/v0.10.28`.

List installed versions:

    $ nvm ls
        .nvm
    v0.10.28

Use a specific Node version:

    $ which node
    /usr/local/bin/node
    $ node -v
    v0.11.13
    $ nvm use 0.10.28
    Now using node v0.10.28
    $ node -v
    v0.10.28
    $ which node
    /home/chao/.nvm/v0.10.28/bin/node

Basically, NVM modified the search path.

    $ echo $PATH
    /home/chao/.nvm/v0.10.28/bin

To roll back:

    $ nvm deactivate
    /home/chao/.nvm/*/bin removed from $PATH
    /home/chao/.nvm/*/share/man removed from $MANPATH
    /home/chao/.nvm/*/lib/node_modules removed from $NODE_PATH

List Node versions available to install:

    $ nvm ls-remote

Use `.nvmrc` file:

    $ echo '0.10.28' >> .nvmrc
    $ nvm use
    Found '/home/chao/.nvmrc' with version <0.10.28>
    Now using node v0.10.28
    $ node -v
    v0.10.28


[nvm]: https://github.com/creationix/nvm
[node]: http://nodejs.org/

