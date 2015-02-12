title: "Docker Save, Load and Deploy"
date: 2015-02-04 12:00:00
categories: [Tools]
tags: [docker, save, load]
---

Need to deploy private Docker containers without a private registry? Try `docker save` and `docker load`.

Working set:

```sh
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
realguess/jq        latest              1f3f837970bf        3 months ago        6.107 MB
realguess/jq        1.4                 6071e18eae76        3 months ago        6.107 MB
busybox             ubuntu-14.04        f6169d24347d        6 weeks ago         5.609 MB
```

Actions to perform:

1. Save an image with a single tag
2. Save an image with multiple tags
3. Save multiple images

Save a single tagged image:

```sh
$ docker save realguess/jq:latest > realguess-jq-latest.tar
```

Save a single image with all tags:

```sh
$ docker save realguess/jq > realguess-jq.tar
```

The tagged one is slightly less in size:

```sh
$ ls -lh realguess*.tar
-rw-rw-r-- 1 chao chao 6.0M Feb 1 12:00 realguess-jq-latest.tar
-rw-rw-r-- 1 chao chao 6.5M Feb 1 12:00 realguess-jq.tar
```

Save multiple images:

```sh
$ docker save busybox realguess/jq > busy-realguess-jq-box.tar
```

The size is almost twice as much comparing to a single image tar:

```sh
$ ls -lh busy*
-rw-rw-r--  1 chao chao  12M Feb 1 12:00 busy-realguess-jq-box.tar
```

Compress it:

```sh
$ out=busy-realguess-jq-box.tar && docker save busybox realguess/jq > $out && gzip $out
```

Much better in size:

```sh
$ ls -lh busy*
-rw-rw-r-- 1 chao chao 5.7M Feb 1 12:00 busy-realguess-jq-box.tar.gz
```

Do the reverse, load the tarred Docker images with [docker load].

[docker load]: https://docs.docker.com/reference/commandline/cli/#load
