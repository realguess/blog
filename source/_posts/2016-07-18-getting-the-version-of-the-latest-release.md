title: Getting the Version of the Latest Release
date: 2016-07-18 12:00:00
category: [Data Science]
tags: [version, docker, curl, grep, cut, cli]
---

What's the latest release of [Docker]?

Its homepage doesn't tell you anything. Have to poke around, click on a few links, may or may not get you what you want. If there's a quick way, even better a CLI method, that will be great.

Couple things we can do. First, when [installing docker], we use the URL <https://get.docker.com/>. It has a path that will return an installation instruction with the version number:

```
$ curl https://get.docker.com/builds/
# To install, run the following command as root:
curl -sSL -O https://get.docker.com/builds/Linux/x86_64/docker-1.11.2.tgz && sudo tar zxf docker-1.11.2.tgz -C /
# Then start docker in daemon mode:
sudo /usr/local/bin/docker daemon
```

There is another way. *Well, there is always another way.* Docker project is hosted in [GitHub], we can use this URL:

```
https://github.com/docker/docker/releases/latest
```

which will be redirected to the latest release:

```
https://github.com/docker/docker/releases/tag/v1.11.2
```

Since it's a redirect, we can use [HTTP HEAD] method without download the entire response body:

```
$ curl --silent --head https://github.com/docker/docker/releases/latest
HTTP/1.1 302 Found
Server: GitHub.com
Content-Type: text/html; charset=utf-8
Status: 302 Found
Cache-Control: no-cache
Vary: X-PJAX
Location: https://github.com/docker/docker/releases/tag/v1.11.2
Vary: Accept-Encoding
```

Extract and process the value of the `Location` field will get us what we are looking for.

Let's construct a simple command to obtain such an information:

```
$ curl                                                       \
    --silent                                                 \
    --head                                                   \
    --url https://github.com/docker/docker/releases/latest | \
  grep                                                       \
    --regexp=^Location                                     | \
  cut                                                        \
    --delimiter=/                                            \
    --fields=8
```

or:

```
$ curl -sI https://github.com/docker/docker/releases/latest | \
    grep ^Location | \
    cut -d / -f 8
```

Both commands will return `v1.11.2`.

By using GitHub, not only we can get the latest stable release version of Docker, we can also obtain other projects. In fact, if the project was hosted in GitHub, and it was tagged properly with the releases, you can use this method to obtain the version. However, if it's not properly tagged, such as [Node.js], you need to find another way.

<!-- more -->

Turning the command into a script[^1]:

```
#!/usr/bin/env bash
#
# Get the latest release version of a repository from GitHub.
#
# Usage: `$ github-version owner/repo` (e.g., `$ github-version docker/docker`)

repo=$1
url=https://github.com/${repo}/releases/latest

curl -sI ${url} | grep ^Location | cut -d / -f 8
```

Usage:

```
$ github-version docker/docker
v1.11.2
$ github-version twbs/bootstrap
v3.3.6
```

One more thing, you can also use the GitHub API:

```
$ curl                                                                  \
    --silent                                                            \
    --url https://api.github.com/repos/twbs/bootstrap/releases/latest | \
  jq .name
```

Settings:

```
$ curl --version
curl 7.35.0

$ jq --version
jq version 1.3

$ grep --version
grep (GNU grep) 2.16

$ cut --version
cut (GNU coreutils) 8.21
```

[^1]: Local: `gists/bin/blob/master/github-version`
[docker]: https://www.docker.com/
[github]: https://github.com/
[http head]: https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html
[installing docker]: /2015/09/07/install-docker-on-ubuntu-trusty-14-04/
[node.js]: https://github.com/nodejs/node
