title: Fail to Upload Large File to Google App Engine Managed VMs
date: 2015-06-25 12:00:00
categories: [DevOps]
tags: [app-engine, managed-vms, gcloud, upload, nginx, docker]
---

After deploying a Node.js app with custom runtime to Google [App Engine] [Managed VMs], when uploading file larger than 1MB, the server returns HTTP 413 status code, meaning the server is refusing to process the request because the uploading entity size is too large.


I had the following setup:

 - The App Engine application is Google managed
 - The application is Node.js app with custom [Dockerfile]
 - The application Docker container runs `node` command directly on port 8080

After accessing the server (which changed from Google managed to user managed), and checked the Docker containers:

```sh
$ sudo docker ps -a
gcr.io/google_appengine/nginx-proxy:latest      "/usr/sbin/nginx"
```

It looks like Nginx is running as the proxy to Node.js application. When uploading file that is larger than 1MB, the response is HTTP 413 error:

```html
<html>
    <head>
        <title>413 Request Entity Too Large</title>
    </head>
    <body bgcolor="white">
        <center>
            <h1>413 Request Entity Too Large</h1>
        </center>
        <hr>
            <center>nginx</center>
        </body>
    </html>
```

And the Nginx access log from the server:

```sh
$ tail /var/log/nginx/error.log 
2015/06/25 12:00:00 [error] 7#0: *101623 client intended to send too large body: 1696805 bytes
```

By default, Nginx limits uploading size or `client_max_body_size` to be 1MB. The default size is just too small. A short video upload could easily exceed the limit. So, how to accept file upload larger than 1MB via Google Managed VMs in App Engine? Is there a way to change the value through `app.yaml`custom runtime configuration file? Or a custom Nginx Docker container can be deployed along with the application Docker container?

<!-- more -->

I have filed an [issue] over the weekend, it was resolved fairly quickly by the next Monday. The issue is fixed by raising the `POST` limit in Nginx to 32MB.

But it is still not possible to deploy multiple Docker containers into the same Managed VM, for example, deploying an app Docker container and an Nginx Docker container to the same VM specified by `app.yaml` file. By doing this way, I can even further customize the Nginx proxy for the app. However, this is not possible now.


[App Engine]: https://cloud.google.com/appengine/docs/
[Managed VMs]: https://cloud.google.com/appengine/docs/managed-vms/
[Dockerfile]: https://docs.docker.com/reference/builder/
[issue]: https://code.google.com/p/googleappengine/issues/detail?id=12032
