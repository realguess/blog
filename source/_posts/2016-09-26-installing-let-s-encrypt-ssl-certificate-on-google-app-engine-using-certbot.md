title: "Installing Let's Encrypt SSL Certificate on Google App Engine Using Certbot"
date: 2016-09-26 12:00:00
categories: [DevOps]
tags: [gae, google-app-engine, certbot, ssl, lets-encrypt, install, nginx]
---

[Let's Encrypt] is a free, open, and automated certificate authority. And its [Certbot] is a fully-featured, extensible client for Let's Encrypt CA that can automate the tasks of getting, renewing and even installing SSL certificates.

Sounds great! However, not yet to be simple and automated, especially working cloud providers such as [Google Cloud Platform] and its [Google App Engine] or GAE.

*But it's free. Yes, it's free. Free software works better. Free certificate authority works better than others.*

GAE is a managed service. The place to stored SSL certificate is in separate machines (load balancers). The current automated domain validation by Certbot mostly work with a single machine. Therefore, when the machine issues certificate request is not the same machine to be validated, we need find another way, hopefully an automated method to perform domain validation across machines.

Before creating an automated method, let's see if we can do it manually. Certbot supports a number of different plugins that can be used to obtain and/or install certificates. A plugin is like an extension that supports a particular web server. Let's see if we can find a plugin that supports GAE.

Here are some supported by Certbot:

```plain
$ certbot --help plugins

plugins:
  Certbot client supports an extensible plugins architecture. See 'certbot
  plugins' for a list of all installed plugins and their names. You can
  force a particular plugin by setting options provided below. Running
  --help <plugin_name> will list flags specific to that plugin.

  --apache              Obtain and install certs using Apache (default: False)
  --nginx               Obtain and install certs using Nginx (default: False)
  --standalone          Obtain certs using a "standalone" webserver. (default:
                        False)
  --manual              Provide laborious manual instructions for obtaining a
                        cert (default: False)
  --webroot             Obtain certs by placing files in a webroot directory.
                        (default: False)
```

And there are also a number of third-party plugins, see the User Guide in [Certbot Documentation]. But there is none for GAE. It looks like there are only three possible options to try: standalone, webroot and manual.

Let's start with the standalone method, and issue that from the local machine:

```plain
$ sudo certbot certonly --standalone -d example.com
```

If you're the first time running the command, you will be prompted for email and agreement screens. Both email and agreement can be *automated* via `--email` and `--agree-tos` options. That's the *automated* part.

After freeing up the ports 80 and 443, run into some issues:

```plain
Failed authorization procedure. example.com (tls-sni-01): urn:acme:error:connection 
:: The server could not connect to the client to verify the domain :: Failed to 
connect to 0.0.0.0:443 for TLS-SNI-01 challenge, example.com (tls-sni-01): 
urn:acme:error:connection :: The server could not connect to the client to verify the 
domain :: Failed to connect to 0.0.0.0:443 for TLS-SNI-01 challenge

IMPORTANT NOTES:
 - The following errors were reported by the server:

   Domain: example.com
   Type:   connection
   Detail: Failed to connect to 0.0.0.0:443 for TLS-SNI-01
   challenge

   To fix these errors, please make sure that your domain name was
   entered correctly and the DNS A record(s) for that domain
   contain(s) the right IP address. Additionally, please check that
   your computer has a publicly routable IP address and that no
   firewalls are preventing the server from communicating with the
   client. If you're using the webroot plugin, you should also verify
   that you are serving files from the webroot path you provided.
```

The standalone plugin runs its own simple web server to prove that you control the domain. Ownership or domain validation is the key here. It needs the current computer that just issued the `certbot` command to have a publicly routable IP address. That's not going to be happening in my local computer behind NAT. And webroot plugin needs a running web server. It can't be run from the local machine as well. Domain validation are done automatically with both `standalone` and `webroot` plugins. Furthermore, domain validation requests are coming from Let's Encrypt servers, therefore, you can't have the machine issuing the certificate request behind a NAT or load balancing methods without properly routing the requests.

Since automated methods mostly require the requester and domain owner to be residing on the same machine, we can try to move the request to the Google cloud. Otherwise, there is one more plugin to try, the manual plugin. The manual method (plugin) *helps you obtain a cert by giving you instructions to perform domain validation yourself*.

<!-- more -->

Application deployed in the GAE Managed VMs is inside a Docker container. So, all needed is to add a router to accept the domain validation challenge. Then, you can issue the request from one machine, have it validated in another machine.

Let's first start the certificate request from the local machine:

```plain
$ sudo certbot certonly --manual --manual-public-ip-logging-ok --domain example.com
Make sure your web server displays the following content at
http://example.com/.well-known/acme-challenge/NmJhODAwYmRmMjdhNzEzMTczOGRjNDFhOGQ0MThmZTgzMDIx before continuing:

Vm1wR1UxTXhXWGxXYkdoV1lUSm9WVmx0ZUhkamJGWlhXWHBTVUZaVk5YVlZSbEYzVTNkdlBRbz0K

If you don't have HTTP server configured, you can run the following
command on the target server (as root):

mkdir -p /tmp/certbot/public_html/.well-known/acme-challenge
cd /tmp/certbot/public_html
printf "%s" Vm1wR1UxTXhXWGxXYkdoV1lUSm9WVmx0ZUhkamJGWlhXWHBTVUZaVk5YVlZSbEYzVTNkdlBRbz0K > \
  .well-known/acme-challeng/NmJhODAwYmRmMjdhNzEzMTczOGRjNDFhOGQ0MThmZTgzMDIx
# run only once per server:
$(command -v python2 || command -v python2.7 || command -v python2.6) -c \
"import BaseHTTPServer, SimpleHTTPServer; \
s = BaseHTTPServer.HTTPServer(('', 80), SimpleHTTPServer.SimpleHTTPRequestHandler); \
s.serve_forever()"
Press ENTER to continue
```

The flag `--manual-public-ip-logging-ok` automatically allows public IP logging. Why logging IP address?

> Let's Encrypt privacy policy states that we will retain and publish IP addresses associated with ACME validation requests, including requestor IP.[^1]

There are a few options to complete the challenge. We can setup a module to handle the request with `dispatch.yaml` file to route the ACME challenge requests to the module only, or we can update our application to support the challenge. Former is better, because it keeps things separated.

But if you have only one instance running, you can do a quick hack. GAE has a Nginx container proxy all the incoming requests. We can simply SSH into the container, add the challenge response inside Nginx container.

SSH into the GAE instance:

```plain
$ gcloud compute ssh chao@myinstance --project myproject
```

Find the Nginx container, which has the image name ended with `nginx-proxy`, and get into the running container:

```plain
$ sudo docker exec -it NAME /bin/bash
```

Inside the container, you might need an editor:

```plain
root@server:/etc/nginx# apt-get update && apt-get install -y vim
```

Edit the file:

```plain
root@server:/etc/nginx# vi nginx.conf
```

Add the following directory just before ones dealing with the health check:

```nginx
location /.well-known/acme-challenge {
  root /etc/nginx;
}

location = /_ah/health {
```

Create the supporting file structure and add the challenge response:

```plain
root@server:/etc/nginx# mkdir -p .well-known/acme-challenge
root@server:/etc/nginx# echo -n 'Vm1wR1UxTXhXWGxXYkdoV1lUSm9WVmx0ZUhkamJGWlhXWHBTVUZaVk5YVlZSbEYzVTNkdlBRbz0K' > \
  .well-known/acme-challenge/NmJhODAwYmRmMjdhNzEzMTczOGRjNDFhOGQ0MThmZTgzMDIx
root@server:/etc/nginx# tree .well-known/      
.well-known/
`-- acme-challenge
    `-- NmJhODAwYmRmMjdhNzEzMTczOGRjNDFhOGQ0MThmZTgzMDIx
```

Reload Nginx:

```plain
root@server:/etc/nginx# nginx -s reload
```

Check out the URL yourself, make sure it is working. And finally, back to the local terminal, and press `<Enter>` key:

```plain
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your cert will
   expire on 2016-12-25. To obtain a new or tweaked version of this
   certificate in the future, simply run certbot again. To
   non-interactively renew *all* of your certificates, run "certbot
   renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

The public key is `fullchain.pem`, and the private key can be obtained:

```plain
$ openssl rsa -in /etc/letsencrypt/live/example.com/privkey.pem -out rsa.pem
```

Upload to <https://console.cloud.google.com/appengine/settings/certificates>, where:

- PEM encoded X.509 public key certificate: `fullchain.pem`
- Unencrypted PEM encoded RSA private key: `rsa.pem`

There you go. Now you just installed a SSL certificate issued by [Let's Encrypt] via [Certbot] using local request and remote validation method.

Next, let's make it an automated solution.


[^1]: <https://github.com/certbot/certbot/issues/991>
[Certbot]: https://certbot.eff.org/
[Certbot Documentation]: https://certbot.eff.org/docs
[Google App Engine]: https://appengine.google.com/
[Google Cloud Platform]: https://cloud.google.com/
[Let's Encrypt]: https://letsencrypt.org/
