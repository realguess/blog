title: "Renewing Let's Encrypt SSL Certificate with Docker"
date: 2016-10-09 12:00:00
categories: [DevOps]
tags: [renew, docker, certbot, lets-encrypt, ssl]
---

[Let's Encrypt CA] issues short-lived certificates (90 days). Automated renewal process is preferred, recommended, and encouraged. But in a few situations, automated process is not available, here is how to do it manually when [SSL certificate was installed with Docker][install]:

First, update the container to the latest version. The latest version can be found from the [release page] in GitHub.

The latest is `v0.9.1`:

```plain
$ docker pull quay.io/letsencrypt/letsencrypt:v0.9.1
```

Turn off application (if running as a Docker container) to free up the HTTPS port 443:

```sh
$ docker stop app
```

Renew the certificate by issuing `renew` command:

```plain
$ docker run -it --rm -p 443:443 --name certbot \
  -v /etc/letsencrypt:/etc/letsencrypt          \
  -v /var/log/letsencrypt:/var/log/letsencrypt  \
  quay.io/letsencrypt/letsencrypt:v0.9.1 renew
Saving debug log to /var/log/letsencrypt/letsencrypt.log

-------------------------------------------------------------------------------
Processing /etc/letsencrypt/renewal/example.com.conf
-------------------------------------------------------------------------------
Cert is due for renewal, auto-renewing...
Starting new HTTPS connection (1): acme-v01.api.letsencrypt.org
Renewing an existing certificate
Performing the following challenges:
tls-sni-01 challenge for example.com
Waiting for verification...
Cleaning up challenges
Generating key (2048 bits): /etc/letsencrypt/keys/0001_key-certbot.pem
Creating CSR: /etc/letsencrypt/csr/0001_csr-certbot.pem

-------------------------------------------------------------------------------
new certificate deployed without reload, fullchain is
/etc/letsencrypt/live/example.com/fullchain.pem
-------------------------------------------------------------------------------

Congratulations, all renewals succeeded. The following certs have been renewed:
  /etc/letsencrypt/live/example.com/fullchain.pem (success)
```

Restart app (it's `start` not `restart`):

```sh
$ docker start app
```

Check the expiration date:

```plain
$ echo | openssl s_client -connect example.com:443 2> /dev/null | \
  openssl x509 -noout -dates
notBefore=Oct  9 12:00:00 2016 GMT
notAfter=Jan  7 12:00:00 2017 GMT
```

For more information on renewing, see the [Renewing Certificates] section from the Certbot documentation.

Settings:

- Certbot v0.9.1
- Docker v1.12.1


[install]: https://realguess.net/2016/10/09/getting-let-s-encrypt-ssl-certificate-with-docker/
[Let's Encrypt CA]: https://letsencrypt.org/
[release page]: https://github.com/certbot/certbot/releases
[Renewing Certificates]: https://certbot.eff.org/docs/using.html#renewing-certificates
