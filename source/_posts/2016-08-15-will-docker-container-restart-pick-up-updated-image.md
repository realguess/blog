layout: image
title: Will Docker Container Restart Pick Up Updated Image?
date: 2016-08-14 12:00:00
categories: [DevOps]
tags: [restart, docker]
---

When a [Docker] image has been updated, will restarting the running container via `docker restart` pick up the change? Educated guess will be *no*, because like restarting a process, the memory is still retained. The best way to find out is to give a try.

Let's start with a [Dockerfile]:

```
# Version Foo
FROM debian:8.5
CMD while true; do echo foo; sleep 5; done
```

The command will keep printing `foo` every 5 seconds.

Create the image:

```
$ docker build -t example .
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM debian:8.5
 ---> 1b088884749b
Step 2 : CMD while true; do echo foo; sleep 5; done
 ---> Running in 38fdeb15f629
 ---> 6a56a50ef254
Removing intermediate container 38fdeb15f629
Successfully built 6a56a50ef254
```

Notice the image ID starting with `6a56`.

Start the container:

```
$ docker run -d --name example example
dac42e7194e4ec2bdca8e24db29a3333ae2f422d316e341c5cb1499034a4357b
```

Check the log:

```
$ docker logs example
foo
foo
```

This is expected output.

Inspect the container:

```
$ docker inspect example
```

The important field is the corresponding image, which matches to the previous built image:

```
{
  ...
  "Image": "sha256:6a56a50ef254bb1d07117b0a0750ef81fafe9735ab3b0f2b0a14511f38d5b83d"
  ...
}
```

Now update the Dockerfile:

```
# Version Bar
FROM debian:8.5
CMD while true; do echo bar; sleep 5; done
```

This time it prints `bar` instead of `foo`.

Rebuild the image:

```
$ docker build -t example .
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM debian:8.5
 ---> 1b088884749b
Step 2 : CMD while true; do echo bar; sleep 5; done
 ---> Running in 7fc297e12005
 ---> a6c04345afb9
Removing intermediate container 7fc297e12005
Successfully built a6c04345afb9
```

Now we have a different image. The image ID is different: `a6c0`. But the old image is still there:

```
$ docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
example                           latest              a6c04345afb9        24 seconds ago      125.1 MB
<none>                            <none>              6a56a50ef254        3 minutes ago       125.1 MB
```

Restart the container:

```
$ docker restart example
example
```

Got `bar`? No still `foo` all the way with the log. And when you inspect the container, it still uses the old image.

So, `docker restart` will **not** pick up the changes from updated image, it will still use the old image built previously. Therefore, the correct way is to drop the container entirely and run it again:

```
$ docker stop example && docker rm example && docker run -d --name example example
example
example
55cec9110fed0257060673a085a08f143003336b1720894f43c6ac5a22104935
```

The log shows the correct message:

```
$ docker logs example
bar
bar
```

Inspecting the container, now it has the correct image:

```
$ docker inspect example
{
  ...
  "Image": "sha256:a6c04345afb953ab392241f56c04f72110c772a6ee3a36e248c1ffd03f81b7d6"
  ...
}
```

And don't forget to delete the old image.

Settings:

```
$ docker --version
Docker version 1.12.0, build 8eab29e
```


[Docker]: https://docker.com/
[Dockerfile]: https://docs.docker.com/engine/reference/builder/
