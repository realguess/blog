Tmux
====

Goals
-----

1. **Beef up my development environment**
2. Create a persistent dashboard for monitoring servers.


Todos
-----

1. VI: Insert current date in the cursor position.
2. Tmux homepage has a link to GitHub with lots of goodies.


Reading List
------------

1. Brian P. Hogan, Tmux (started on 2013-12-07)
2. man tmux


Progress
--------

-  [2014-01-04] Paired programming is so cool. I need to review it and have it
   setup on one of our development server.
   Skipped because I do not have a reliable Internet connection.
-  [2014-01-04] Book "Tmux" chapter 6.1 Working Effectively with Panes and
   windows
-  [2013-12-28] Book "Tmux" chapter 4.3 Working with the clipboard on Linux
-  ~[2013-12-28] Book "Tmux" chapter 3.4 What is next?~
-  ~[2013-12-17] Book "Tmux" chapter 3.2 Using tmux configuration for setup~
-  ~[2013-12-12] Book "Tmux" chapter 3.1 Creating a Custom Setup~
-  ~[2013-12-10] Book "Tmux" chapter 2.3 Visual Styling~
-  ~[2013-12-08] Book "Tmux" chapter 2.2 Customizing...~
-  ~[2013-12-07] Book "Tmux" chapter 1.4 Working with Windows.~


Tips
----

**Sessions**

+------------------------------------------------------------------------------+
| Title                                | Description                       | G |
+------------------------------------------------------------------------------+
| Get version                          | $ tmux -V                         | A |
|--------------------------------------|-----------------------------------|---|
| Command prefix                       | C-b                               | A |
|--------------------------------------|-----------------------------------|---|
| Create a named session               | $ tmux new-session -s mysession   | F |
|                                      | $ tmux new -s mysession           |   |
|                                      |                                   |   |
|                                      | Named session is very handy for   |   |
|                                      | leaving it running in the bg.     |   |
|--------------------------------------|-----------------------------------|---|
| Create a new background session      | $ tmux new -s bgsession -d        | F |
|--------------------------------------|-----------------------------------|---|
| Detach a session                     | C-b d                             | F |
|--------------------------------------|-----------------------------------|---|
| List all sessions                    | $ tmux ls                         | F |
|                                      | $ tmux list-sessions              |   |
|--------------------------------------|-----------------------------------|---|
| Attach the most recently  detached   | $ tmux attach                     | F |
| session                              | $ tmux attach-session             |   |
|--------------------------------------|-----------------------------------|---|
| Kill a session                       | $ tmux kill-session -t mysession  | F |
|--------------------------------------|-----------------------------------|---|
| Name the first window upon creation  | $ tmux new -s mysession -n mywin  | F |
| of a new session                     |                                   |   |
|--------------------------------------|-----------------------------------|---|
| List sessions for quick navigation   | s                                 | F |
+------------------------------------------------------------------------------+


**Windows**

+------------------------------------------------------------------------------+
| Title                                | Description                       | G |
+------------------------------------------------------------------------------+
| Create a window                      | c                                 | A |
|--------------------------------------|-----------------------------------|---|
| Rename a window                      | ,                                 | A |
|--------------------------------------|-----------------------------------|---|
| Move to next window                  | n                                 | F |
|--------------------------------------|-----------------------------------|---|
| Move to previous window              | p                                 | F |
|--------------------------------------|-----------------------------------|---|
| Move to window 0                     | 0                                 | A |
|--------------------------------------|-----------------------------------|---|
| Find a window by name                | f                                 | D |
|--------------------------------------|-----------------------------------|---|
| Display a list of windows            | w                                 | F |
|--------------------------------------|-----------------------------------|---|
| Kill a window                        | &                                 | F |
+------------------------------------------------------------------------------+


**Panes**

+------------------------------------------------------------------------------+
| Title                                | Description                       | G |
+------------------------------------------------------------------------------+
| Split the window vertically          | %                                 | A |
|--------------------------------------|-----------------------------------|---|
| Split the window horizontally        | '"' (single double quote)         | A |
|--------------------------------------|-----------------------------------|---|
| Cycle through panes                  | o                                 | A |
|--------------------------------------|-----------------------------------|---|
| Move around the panes                | Up, down, Left Right              | A |
|--------------------------------------|-----------------------------------|---|
| Cycle though pane layouts            | <space>                           | D |
|--------------------------------------|-----------------------------------|---|
| Kill a pane                          | x                                 | F |
|--------------------------------------|-----------------------------------|---|
| Send command to a target pane 2 in   | :send-keys -t session.1.2 'top'   | F |
| window 1 of session 'session'        |  C-m                              |   |
|--------------------------------------|-----------------------------------|---|
| Pop a pane out into a new window     | !                                 | F |
|--------------------------------------|-----------------------------------|---|
| Turn a window into a pane            | :join-pane -s 2 -t 1              | F |
|--------------------------------------|-----------------------------------|---|
|                                      |                                   |   |
+------------------------------------------------------------------------------+


**Commands**

+------------------------------------------------------------------------------+
| Title                                | Description                       | G |
+------------------------------------------------------------------------------+
| Enter command mode                   | :                                 | A |
|--------------------------------------|-----------------------------------|---|
| Create a new window                  | :new-window -n mywindow           | F |
|--------------------------------------|-----------------------------------|---|
| Create a new window and run a        | :new-window -n mywindow [command] | F |
| command and command only, after      |                                   |   |
| exiting, the window is killed        |                                   |   |
|--------------------------------------|-----------------------------------|---|
| Create a new pane                    | TODO                              |   |
|--------------------------------------|-----------------------------------|---|
| Shortcut for `set-option`            | set                               | F |
|--------------------------------------|-----------------------------------|---|
| Shortcut for `set-window-option`     | setw                              | F |
|--------------------------------------|-----------------------------------|---|
| Set window index starting from 1     | :set base-index 1                 | F |
|--------------------------------------|-----------------------------------|---|
| Display a message on status bar      | :display-message "Hello message"  | F |
|                                      | :display "Hello message"          |   |
|--------------------------------------|-----------------------------------|---|
| Combine commands (\;)                | :display "Hello" \; display "Hi"  | F |
|                                      | FIXME: Fail to work               |   |
|--------------------------------------|-----------------------------------|---|
| Send keys with carriage return       | :send-keys 'top' C-m              | F |
|--------------------------------------|-----------------------------------|---|
| Toggle piping out to a file          | :pipe-pane -o "cat >> ~/log.txt"  | F |
|--------------------------------------|-----------------------------------|---|
|                                      |                                   |   |
+------------------------------------------------------------------------------+


**Buffer Mode**
+------------------------------------------------------------------------------+
| Title                                | Description                       | G |
+------------------------------------------------------------------------------+
| Enter copy mode                      | :copy-mode                        | F |
|                                      | [                                 |   |
|--------------------------------------|-----------------------------------|---|
| Jump to top/bottom of the buffer     | g/G                               | F |
|--------------------------------------|-----------------------------------|---|
| Search through the buffer            | ?                                 | F |
|--------------------------------------|-----------------------------------|---|
| Copy text                            | <space>jl<enter>                  | F |
|--------------------------------------|-----------------------------------|---|
| Paste buffer                         | :paste-buffer                     | F |
|                                      | ]                                 |   |
|--------------------------------------|-----------------------------------|---|
| Capture the contents of a pane       | :capture-pane                     | F |
|--------------------------------------|-----------------------------------|---|
| Show the contents of paste buffer    | :show-buffer                      | F |
|--------------------------------------|-----------------------------------|---|
| Save the current buffer to a file    | :save-buffer ~/buffer.txt         | F |
|--------------------------------------|-----------------------------------|---|
| List buffers                         | :list-buffers                     | F |
|--------------------------------------|-----------------------------------|---|
| Choose a buffer to paste             | :choose-buffer                    | F |
|                                      | =                                 |   |
|--------------------------------------|-----------------------------------|---|
|                                      |                                   |   |
+------------------------------------------------------------------------------+


**Customization**

+------------------------------------------------------------------------------+
| Title                                | Description                       | G |
+------------------------------------------------------------------------------+
| Set prefix                           | :set -g prefix C-q                | D |
|--------------------------------------|-----------------------------------|---|
| Send prefix to other applications    | :bind C-q send-prefix             | F |
|--------------------------------------|-----------------------------------|---|
| Set vertical pane split key          | :bind \ splitw -h                 | F |
|--------------------------------------|-----------------------------------|---|
| Set horizontal pane split key        | :bind - splitw -v                 | F |
|--------------------------------------|-----------------------------------|---|
|                                      |                                   |   |
+------------------------------------------------------------------------------+


**Others**

+------------------------------------------------------------------------------+
| Title                                | Description                       | G |
+------------------------------------------------------------------------------+
| Display key bindings                 | ?                                 | F |
| TODO: Can I use VI to view it?       |                                   |   |
|--------------------------------------|-----------------------------------|---|
| Execute commands in a file           | :source-file ~/.tmux.conf         | F |
|--------------------------------------|-----------------------------------|---|
| Reload the configuration file        | :source-file ~/.tmux.conf         | F |
|--------------------------------------|-----------------------------------|---|
|                                      |                                   |   |
+------------------------------------------------------------------------------+


Customization
-------------

Configuration settings search path:

-  `/etc/tmux.conf` (but in Ubuntu, it is not installed here, and how do I look
   for where the files related to tmux in Ubuntu?
-  `~/.tmux.conf`


Pair Programming with Tmux
--------------------------

- Low bandwidth requirement.

How? _Use Tmux sockets to have a second user connect to my tmux session._

Actually just need to use two terminal locally.

Open one xterm, the problem is that only one user is using it. We need multiple users.

1. Shared account:

Share everything (windows and panes):

    $ tmux attach -t shared-session

Share session but not windows or panes:

    $ tmux new-session -t shared-session -s my-session


Shortips
--------

-  Support multiple languages
-  Must have social sharing feature
-  Must have private/public feature
-  Two levels: [book, chapter]
-  Tags are tags, just another way to organize things.
-  The `title` and `description` does not limit to any size, you can make it as
   long as you want, but only the first line will be shown in this table display
   format. Therefore, try to limit yourself.

Each tip is formatted in YAML.

Oh year, make this a course to offer in China, what a brilliant idea! Because
you can work it over SMS. This works on vocabulary as well. Just have two sets
of documents, one for content, another for status. Such as HTTP header and
content, meta data and actual data.
