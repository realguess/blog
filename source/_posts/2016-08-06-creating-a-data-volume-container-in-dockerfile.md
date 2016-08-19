title: Creating a Data Volume Container in Dockerfile
date: 2016-08-06 12:00:00
categories: [DevOps]
tags: [docker, data-volume, dockerfile]
---

Create a [Docker] data volume container in [Dockerfile] is unbelievably simple, just use the [VOLUME] instruction:

```ini
FROM debian:8.5
VOLUME ["/data"]
```

The instruction creates a mount point and attach the volumes from native host or other containers.

Build the data container:

```
$ docker build -t data .
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM debian:8.5
 ---> 1b088884749b
Step 2 : VOLUME /data
 ---> Running in 5511f34a489c
 ---> 7b723b2b3d13
Removing intermediate container 5511f34a489c
Successfully built 7b723b2b3d13
```

The built size is just about 125.1 MB.

```
$ docker create --name data data
```

The first `data` is the name of the container, the second `data` is the name of Docker image.

To attach the data volume container to another, we use `--volumes-from` option:

```
$ docker run -it --rm --name foo --volumes-from=data debian:8.5 /bin/bash
```

If there're initial data to copy, then add the `COPY` instruction:

```ini
FROM debian:8.5
VOLUME ["/data"]
COPY . /data
```

Settings:

```
$ docker --version
Docker version 1.11.2, build b9f10c9
```


[Docker]: https://docker.com/
[Dockerfile]: https://docs.docker.com/engine/reference/builder/
[VOLUME]: https://docs.docker.com/engine/reference/builder/#/volume
