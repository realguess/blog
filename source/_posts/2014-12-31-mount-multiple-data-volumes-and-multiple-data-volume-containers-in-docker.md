title: Mount Multiple Data Volumes and Multiple Data Volume Containers in Docker
date: 2014-12-31 12:00:00
categories: [Tools]
tags: [docker, data-volume, container]
---


**Multiple Data Volumes from a Single Container**

Create and run multiple data volumes in a container:

```sh
$ docker run -d --name vol -v /vol1 -v /vol2 ubuntu
```

Mount the data volumes in a new container:

```sh
$ docker run -it --rm --name foo --volumes-from=vol ubuntu
```

Both volumes will be mounted under the root directory as `/vol1` and `/vol2`.


**Multiple Data Volume Containers**

Create and run multiple data volume containers:

```sh
$ for i in {1..2}; do docker run -d --name vol${i} -v /vol${i} ubuntu; done
```

Mount multiple data volume containers:

```sh
$ docker run -it --rm --name foo --volumes-from=vol1 --volumes-from=vol2 ubuntu
```

Now there are also two volumes mounted under the root directory as `/vol1` and `/vol2`, one from each container.


**Multiple Data Volume Containers But Sharing the Same Data Volume Name**

Create multiple data volume containers with the same data volume name `/vol`:

```sh
$ for i in {1..2}; do docker run -d --name vol${i} -v /vol ubuntu; done
```

Mount multiple data volume containers:

```sh
$ docker run -it --rm --name foo --volumes-from=vol1 --volumes-from=vol2 ubuntu
```

<!-- more -->


Well, it appears to be successful without any error. But there is only one shared data volume `/vol`. The question is which container this data volume is from? To find out, we can create a file inside the data volume and then mount the data volume containers one at a time.

Create a file inside the shared data volume:

```sh
root@:/# touch /vol/file
root@:/# exit
```

Mount data volume container `vol1`:

```plain
$ docker run -it --rm --name foo --volumes-from=vol1 ubuntu
# ls /vol
total 0
# exit
```

Well, it is not in data volume container `vol1`.

Mount data volume container `vol2`:

```plain
$ docker run -it --rm --name foo --volumes-from=vol2 ubuntu
root@:/# ls /vol
file
root@:/# exit
```

It is in data volume container `vol2`. Therefore, when mounting two data volume containers, if both share the same data volume name, later will override former, for example, in this command:

```sh
$ docker run -it --rm --name foo --volumes-from=vol1 --volumes-from=vol2 ubuntu
```

Data volume `vol` from container `vol2` will override the one from `vol1`.
