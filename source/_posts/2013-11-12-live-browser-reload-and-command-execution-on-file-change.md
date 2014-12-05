title: Live Browser Reload and Command Execution on File Change
date: 2013-11-12 12:32:34
categories: [Tools]
tags: [nodemon, docco, grunt-contrib-watch, livereload, guard, todo]
---

Execute Command
---------------

When I am editting comments in my code, I would like to use [Docco][] to generate a pretty print source code documentation and review it in web browser. However, every time I made a change, I had to issue `docco` command again, even I could use the up arrow key, but still a pain. Lucky, there is a way to eliminate this step. One way is to use [grunt-contrib-watch][], but the limitation is that this is not for an individual file on the command line, it is more for a build process. A better alternative is to use [nodemon][]:

> For use during development of a node.js based application. nodemon will watch the files in the directory that nodemon was started, and if they change, it will automatically restart your node application.

It does not have to be limited to Node and JavaScript files. We can use it with any command:

    nodemon -x docco /path/to/app.coffee

With this command, `docco` will be executed upon any change to the file.


Reload Browser
--------------

Making live reload in browser is a little bit trickier, it involves using some [Ruby gems][].

_TODO: Need to find a way to avoid using Ruby and its gems._

I have followed some of the steps from the post: [Auto-refresh your browser when saving files or restarting node.js][1].

First install [LiveReload][] Chrome extension, and then install [Guard][] and [Guard::LiveReload][guard-livereload] gems:

    sudo gem install guard guard-livereload

You need to have a web server to make the livereload work, live reload does not work on files directly served from the file system:

    file:///home/chao/docs/app.html

Therefore, an easy way is to set up a web server, such as Nginx, and configure a directory for this usage, such as:

    /usr/share/nginx/www/livereload

Add the following `Guardfile` into the directory:

    guard 'livereload' do
      watch(%r{.+\.(css|js|ejs|html)})
    end

Launch [Guard][]:

    cd /usr/share/nginx/www/livereload && guard

Enable the live reload by clicking the menu icon. You should see the dot in the middle becomes solid.

Now you need to make sure set the generated files into the correct directory:

    nodemon -x 'docco -o /usr/share/nginx/www/livereload' /path/to/app.coffee

Another way is to create a soft link to the directory, since the `docs` in the current execution directory is the default output directory:

    ln -s /usr/share/nginx/www/livereload docs

Then, you can use [nodemon][] as you normally do:

    nodemon -x docco /path/to/app.coffee

Having live browser reload is a bit complicated. I wish there is a method as easy as using [nodemon][] via a single command. But in the meantime, I just need to have two panes open and have both monitoring tools running independently. No more browser fresh and command re-issuing.

[nodemon]: https://github.com/remy/nodemon
[docco]: http://jashkenas.github.io/docco/
[grunt-contrib-watch]: https://github.com/gruntjs/grunt-contrib-watch
[ruby gems]: http://rubygems.org/
[livereload]: https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei
[guard]: https://github.com/guard/guard
[guard-livereload]: https://github.com/guard/guard-livereload
[1]: https://vickev.com/#!/article/auto-refresh-your-browser-when-saving-files-or-restarting-node-js
