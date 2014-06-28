title: Tiny HTTP Servers
date: 2014-06-21 21:24:54
tags: [http-server, web-server, python, ruby, node]
---

This is a simple way to run a tiny web server via command line:

    $ python -m SimpleHTTPServer 8080

which will server static files from the current directory.

There is [Knod] for Ruby, and of course there is one for Node: [http-server].

To install:

    $ sudo npm install -g http-server

To run:

    $ http-server

Server will be accessible via <http://localhost:8080/>.

[Knod]: https://github.com/moserrya/knod
[http-server]: https://github.com/nodeapps/http-server
