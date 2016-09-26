title: "Installing Let's Encrypt Certbot 0.8.x on Debian Jessie"
date: 2016-09-25 12:00:00
categories: [DevOps]
tags: [certbot, lets-encrypt, ca, certificate-authority, install, debian, debian-jessie, backports]
---

[Let's Encrypt] is a free, open, and automated certificate authority. And its [Certbot] is "a fully-featured, extensible client for the Let's Encrypt CA (or any other CA that speaks the ACME protocol) that can automate the tasks of obtaining certificates and configuring webservers to use them."[^1]

There are a number of ways to obtain and install SSL certificates issued by Let's Encrypt CA. This is about installing Certbot 0.8.0 release on Debian Jessie. But before continuing, a few things to think about:

> The Let's Encrypt Client (Certbot) presently only runs on Unix-ish OSes that include Python 2.6 or 2.7; Python 3.x support will hopefully be added in the future. ... currently it supports modern OSes based on Debian, Fedora, SUSE, Gentoo and Darwin.[^1]

That's why using Docker container installation method might be a better choice, because it does not mess up your existing libraries and it can use supported operating systems which might not be the one you are using.

Anyhow, the current installation settings are:

- Debian 8.5 Jessie
- Python 2.7.9
- Certbot 0.8.0

[Certbot] is available for Debian Jessie via [backports].

> Backports are recompiled packages from testing (mostly) and unstable (in a few cases only, e.g. security updates) in a stable environment so that they will run without new libraries (whenever it is possible) on a Debian stable distribution.
>
> Backports cannot be tested as extensively as Debian stable, and backports are provided on an as-is basis, with risk of incompatibilities with other components in Debian stable. Use with care!
>
> It is therefore recommended to select single backported packages that fit your needs, and not use all available backports.

Again, there's why it might be a better idea to use a container. But, let's proceed.

Add a new file named `backports.list` to `/etc/apt/sources.list.d/` directory:

```plain
$ sudo bash -c 'echo "deb http://ftp.debian.org/debian jessie-backports main" > \
  /etc/apt/sources.list.d/backports.list'
```

Update:

```plain
$ sudo apt-get update
```

All backports are deactivated by default, therefore, to install Certbot package from backports, run:

```plain
$ sudo apt-get install certbot -t jessie-backports
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following extra packages will be installed:
  dialog python-acme python-certbot python-cffi-backend python-configargparse python-configobj python-cryptography
  python-dialog python-enum34 python-funcsigs python-idna python-ipaddress python-mock python-ndg-httpsclient python-openssl
  python-parsedatetime python-pbr python-psutil python-pyasn1 python-pyicu python-requests python-rfc3339 python-six
  python-tz python-urllib3 python-zope.component python-zope.event python-zope.interface
Suggested packages:
  python-certbot-apache python-certbot-doc python-acme-doc python-configobj-doc python-cryptography-doc
  python-cryptography-vectors python-enum34-doc python-funcsigs-doc python-mock-doc python-openssl-doc python-openssl-dbg
  python-psutil-doc doc-base python-ntlm
Recommended packages:
  letsencrypt
The following NEW packages will be installed:
  certbot dialog python-acme python-certbot python-cffi-backend python-configargparse python-configobj python-cryptography
  python-dialog python-enum34 python-funcsigs python-idna python-ipaddress python-mock python-ndg-httpsclient python-openssl
  python-parsedatetime python-pbr python-psutil python-pyasn1 python-pyicu python-requests python-rfc3339 python-tz
  python-urllib3 python-zope.component python-zope.event python-zope.interface
The following packages will be upgraded:
  python-six
1 upgraded, 28 newly installed, 0 to remove and 163 not upgraded.
Need to get 1,881 kB of archives.
After this operation, 10.5 MB of additional disk space will be used.
Do you want to continue? [Y/n]
```

APT option `-t` lets you have simple control over which distribution packages will be retrieved from. In this case, the distribution `jessie-backports` is used.

Interesting to know that there is `letsencrypt` package, could this be the old client? Let's query the APT's package cache:

```plain
$ apt-cache show letsencrypt
Package: letsencrypt
Source: python-certbot
Version: 0.8.0-1~bpo8+2
Installed-Size: 29
Maintainer: Debian Let's Encrypt <letsencrypt-devel@lists.alioth.debian.org>
Architecture: all
Depends: certbot
Description-en: transitional dummy package
 This is a transitional dummy package for the rename of letsencrypt to certbot.
 It can safely be removed.
```

Yes, it's a dummy package. It has been renamed. And from the documentation:

> Until May 2016, Certbot was named simply `letsencrypt` or `letsencrypt-auto`, depending on install method.[^1]

Let's poke around on the installed package:

```plain
$ certbot --version
certbot 0.8.0
```

It's not yet `1.0`.

Obtaining the quick help:

```plain
$ certbot --help

  certbot [SUBCOMMAND] [options] [-d domain] [-d domain] ...

Certbot can obtain and install HTTPS/TLS/SSL certificates.  By default,
it will attempt to use a webserver both for obtaining and installing the
cert. Major SUBCOMMANDS are:

  (default) run        Obtain & install a cert in your current webserver
  certonly             Obtain cert, but do not install it (aka "auth")
  install              Install a previously obtained cert in a server
  renew                Renew previously obtained certs that are near expiry
  revoke               Revoke a previously obtained certificate
  register             Perform tasks related to registering with the CA
  rollback             Rollback server configuration changes made during install
  config_changes       Show changes made to server config during installation
  plugins              Display information about installed plugins

Choice of server plugins for obtaining and installing cert:

  (the apache plugin is not installed)
  --standalone      Run a standalone webserver for authentication
  (nginx support is experimental, buggy, and not installed by default)
  --webroot         Place files in a server's webroot folder for authentication

OR use different plugins to obtain (authenticate) the cert and then install it:

  --authenticator standalone --installer apache

More detailed help:

  -h, --help [topic]    print this message, or detailed help on a topic;
                        the available topics are:

   all, automation, paths, security, testing, or any of the subcommands or
   plugins (certonly, install, register, nginx, apache, standalone, webroot,
   etc.)
```

Now it's time to obtain the certificate.


[^1]: [Certbot Documentation](https://certbot.eff.org/docs)
[backports]: https://backports.debian.org/Instructions/
[Certbot]: https://certbot.eff.org/
[Let's Encrypt]: https://letsencrypt.org/
