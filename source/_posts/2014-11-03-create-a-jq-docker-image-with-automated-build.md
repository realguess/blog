title: Create a jq Docker Image with Automated Build
date: 2014-11-03 22:00:00
categories: [Tools]
tags: [automated-build, docker, jq, busybox, github, bitbucket, trigger, webhook]
---

I have created a [jq] Docker image based on [BusyBox] with [automated builds]. BusyBox is really really small in size, so the jq image I have created is also very small, just a little over 6 MB.

Here is the source code and image repositories:

- <https://github.com/realguess/docker-jq>
- <https://registry.hub.docker.com/u/realguess/jq/>

Building Docker image is pretty straight-forward, just follow the instruction:

<https://docs.docker.com/userguide/dockerrepos/#automated-builds>

I have also created a tag `v1.4` in my GitHub repository to match the release of jq binary. This should also be reflected in Docker registry. After couple tries, here is the build details for adding both `latest` and `1.4` tags:

```
Type     Name     Dockerfile Location   Tag Name
------------------------------------------------
Tag      v1.4     /                     1.4
Branch   master   /                     latest
```

The first two columns match git branch and tag, and the last column reflects Docker tags. See <https://github.com/realguess/docker-jq/tags> and <https://registry.hub.docker.com/u/realguess/jq/tags/manage/>.

This is done by starting an automated build with type of `tag` instead of `branch`. Everything is done via the Docker Hub website.

If I pull down this repository:

```
$ docker pull realguess/jq
```

It should give me two image layers with the two different image IDs, which makes sense, as latest commit usually is not the same as the tagged one.

After a while, the index should be built, and I can search it via:

```
$ docker search jq
NAME             DESCRIPTION              STARS    OFFICIAL   AUTOMATED
realguess/jq                              1                   [OK]
```

The image is listed as `AUTOMATED`, but the description is missing here in the search command.

There are two types of descriptions:

- Short description
- Full description

Full description will be generated automatically via `README.md` file. I thought the short description can also be generated via `README-short.txt`, however, this is not the case. You can add it in the settings page for example:

<https://registry.hub.docker.com/u/realguess/jq/settings/>

Automated builds are triggered automatically with GitHub and BitBucket repository. Once any commit is pushed to either repository, a trigger will be sent to Docker Hub, and automated build will start.


[jq]: http://stedolan.github.io/jq/
[BusyBox]: https://registry.hub.docker.com/_/busybox/
[Automated Builds]: https://docs.docker.com/docker-hub/builds/
