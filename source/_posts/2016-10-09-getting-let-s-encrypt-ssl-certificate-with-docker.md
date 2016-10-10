title: "Getting Let's Encrypt SSL Certificate with Docker"
date: 2016-10-09 12:00:00
categories: [DevOps]
tags: [install, docker, certbot, lets-encrypt, ssl]
---

[Let's Encrypt] is a free, open, and automated certificate authority (CA). And its [Certbot] is a fully-featured, extensible client for Let's Encrypt CA that can automate the tasks of getting, renewing and even installing SSL certificates.

First, you need to get Certbot. There are a few ways to [install Certbot]. But with [Docker], you don't need to install, you just need to download the [Docker image] and run the container. However, the caveat is that this method does not *install* the certificate automatically respecting to your web server. But if you're like me, running your server in another Docker container, this might be the way to go.

Let's start.

First, download the image. You can download the `latest` version (tag):

```sh
$ docker pull quay.io/letsencrypt/letsencrypt:latest
```

But the latest usually is not a stable release:

```plain
$ docker run -it --rm quay.io/letsencrypt/letsencrypt:latest --version
certbot 0.10.0.dev0
```

Therefore, it's better to use a specific release, which can be found in Certbot's GitHub page: <https://github.com/certbot/certbot/releases>.

The latest one now is `v0.9.1`. We can pull that from Quay.io:

```sh
$ docker pull quay.io/letsencrypt/letsencrypt:v0.9.1
```

Confirm the release version:

```plain
$ docker run -it --rm quay.io/letsencrypt/letsencrypt:v0.9.1 --version
certbot 0.9.1
```

Let's take a look at the Docker image:

```sh
$ docker inspect quay.io/letsencrypt/letsencrypt:v0.9.1
```

Dropping things don't care, the output is:

```json
[
    {
        "RepoTags": [
            "quay.io/letsencrypt/letsencrypt:v0.9.1"
        ],
        "ContainerConfig": {
            "ExposedPorts": {
                "443/tcp": {}
            },
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ENTRYPOINT \u0026{[\"certbot\"]}"
            ],
            "Volumes": {
                "/etc/letsencrypt": {},
                "/var/lib/letsencrypt": {}
            },
            "Entrypoint": [
                "certbot"
            ]
        }
    }
]
```

The `ENTRYPOINT` is `certbot` binary:

> An `ENTRYPOINT` allows you to configure a container that will run as an executable.[^1]

And the command line arguments to `docker run` becomes the arguments to `certbot` command. As we saw earlier to obtain the release version by using `--version`.

> You can override the `ENTRYPOINT` instruction using the `docker run --entrypoint` flag.[^1]

For example, to override and run the container without executing the `certbot` command:

```sh
$ docker run -it --rm --name certbot --entrypoint /bin/bash \
  quay.io/letsencrypt/letsencrypt:v0.9.1
```

But we are more concerning about others, such as exposed port and mapped volumes. The exposed port is 443, HTTPS port. The most important volume (directory) is `/etc/letsencrypt`. All generated keys and issued certificates can be found in there. Directory `/var/lib/letsencrypt` is the default working directory, some backup stuff are stored. I have yet to find it useful. However, the logs directory `/var/log/letsencrypt` is not being used. This could be useful if things went haywire.

<!-- more -->

Obtain certificate:

```plain
$ docker run -it --rm -p 443:443 --name certbot \
  -v /etc/letsencrypt:/etc/letsencrypt          \
  -v /var/log/letsencrypt:/var/log/letsencrypt  \
  quay.io/letsencrypt/letsencrypt:v0.9.1 certonly --standalone -d example.com
```

If successful:

```plain
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your cert will expire
   on 2017-01-06. To obtain a new or tweaked version of this
   certificate in the future, simply run certbot again. To
   non-interactively renew *all* of your certificates, run "certbot
   renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

Based on the documenation [Where are my certificates?], both `fullchain.pem` and `privkey.pem` should be used, which are located at the directory `/etc/letsencrypt/live/example.com/`

Now start or restart your Docker app:

```plain
$ docker run -d --name my-app -p 443:443 \
  -v /etc/letsencrypt:/etc/letsencrypt my-image
```

Viola! Next: [Renewing Let's Encrypt SSL Certificate with Docker].

PS, settings:

- Certbot v0.9.1
- Docker v1.12.1


[^1]: [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#/entrypoint), Dockerfile reference
[Let's Encrypt]: https://letsencrypt.org/
[Certbot]: https://certbot.eff.org/
[install Certbot]: https://certbot.eff.org/docs/using.html
[Docker]: https://docker.com/
[Docker image]: https://quay.io/repository/letsencrypt/letsencrypt
[Where are my certificates?]: https://certbot.eff.org/docs/using.html#where-are-my-certificates
[Renewing Let's Encrypt SSL Certificate with Docker]: https://realguess.net/2016/10/09/renewing-let-s-encrypt-ssl-certificate-with-docker/
