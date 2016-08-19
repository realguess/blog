title: Installing jq from Source
date: 2016-08-17 12:00:00
categories: [DevOps]
tags: [jq-1.5, jq, install]
---

Packages built in both Ubuntu and Debian packages lack behind, therefore, to get the latest version of [jq], build from source.

There are a few prerequisites to install:

- GCC
- Make
- Autotools

Both GCC and Make are usually installed if you do development, but not [Autotools]. Luckily, this is easy to fulfill:

```
$ sudo apt-get install automake
```

Install from source:

```
$ sudo git clone https://github.com/stedolan/jq.git
$ cd jq
$ sudo git checkout jq-1.5
$ sudo ./configure && sudo make && sudo make install
```

The installed path is at:

```
$ which jq
jq is /usr/local/bin/jq
```

However, this gives me an unexpected tag:

```
$ jq --version
jq-1.5-dirty
```

<!-- more -->

I have no idea why there is `dirty` suffix, did a quick search, saw the same tag referenced in this issue: <https://github.com/stedolan/jq/issues/916>, but yet to have answer for it. I might be using the wrong commit, but instead of searching and figuring out why, sometimes it's easier to take the different road, if your goal is not to debug. So, went ahead and just downloaded the tarball:

```
$ wget https://github.com/stedolan/jq/releases/download/jq-1.5/jq-1.5.tar.gz
$ tar xzf jq-1.5.tar.gz
$ cd jq-1.5/
$ sudo ./configure && sudo make && sudo make install
```

Now it looks to be something I want:

```
$ jq --version
jq-1.5
```

Try an example:

```
$ echo '{"foo":"bar"}' | jq .
{
  "foo": "bar"
}
```

So far so good. For more information, see <https://stedolan.github.io/jq/download/>.


[jq]: https://stedolan.github.io/jq/
[Autotools]: https://www.gnu.org/software/automake/
