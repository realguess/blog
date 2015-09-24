title: Add a Custom Domain to the Specific Module of Google App Engine Managed VMs
date: 2015-09-24 12:00:00
categories: [Cloud]
tags: [dispatch, module, version, instance, routing, app-engine, gae, managed-vms, google-cloud-platform, gcloud]
---

I would like to add a custom domain to a module other than the default one in [Google App Engine Managed VMs][managed-vms]. For example, I have the following sub-domains:

```plain
Custom domain names | SSL support | Record type | Data                 | Alias
------------------- | ----------- | ----------- | -------------------- | -----
api.example.com     | none        | CNAME       | ghs.googlehosted.com | api
admin.example.com   | none        | CNAME       | ghs.googlehosted.com | admin
```

listed in the application console:

https://console.developers.google.com/project/[project]/appengine/settings/domains

The domain `api.example.com` points to the default module. And I would like the other domain `admin.example.com` to point to the module `admin`. However, by merely adding the custom domain in the settings, both `api.example.com` and `admin.example.com` are pointed to the same module: the default module. Then how to point the custom domain and route the traffic to the `admin` module? The answer is on the _dispatch file_. But first need to understand the concept of modules in Google App Engine.

![Google App Engine Module Hierarchy][module-hierarchy]
_Source: [Google App Engine Docs][1]_

The above chart illustrates the architecture of a Google App Engine application:

- Application: "An App Engine application is made up of one or more modules". \[[1]\]
- Module: "Each module consists of source code and configuration files. The files used by a module represents a version of the module. When you deploy a module, you always deploy a specific version of the module." \[[1]\] "All module-less apps have been converted to contain a single default module." \[[1]\] Every application has a single default module.
- Version: "A particular module/version will have one or more instances."
- Instance: "Each instance runs its own separate executable."

Another concept is the resource sharing:

- "App Engine Modules let developers factor large applications into logical components that can share stateful services and communicate in a secure fashion." \[[1]\]
- "Stateful services (such as Memcache, Datastore, and Task Queues) are shared by all modules in an application." \[[1]\]

Remember that [Google Cloud Platform] is project based. If there are a web application and a mobile application, and they are both making requests to another application, the API. Then there should be three projects. However, in this case, both `api` and `admin` share the same services, such as the same database and file storage. We should put them together in the same application as separate modules.

With that in mind, how to route requests to a specific module? "Every module, version, and instance has its own unique URI (for example, `v1.my-module.my-app.appspot.com`). Incoming user requests are routed to an instance of a particular module/version according to [URL addressing conventions and an optional customized dispatch file][2]." \[[1]\] Since a custom domain is used, we have to use the customized dispatch file. And this is done by creating a dispatch file `dispatch.yml` to route requests based on URL patterns.

```yaml
# Dispatch
# ========
---
dispatch:
  - url: '*/favicon.ico'
    module: default
  - url: 'admin.example.com/*'
    module: admin
```

The `application` field is not necessary, otherwise, you will get:

```sh
WARNING: The [application] field is specified in file [/home/chao/example/dispatch.yml].
This field is not used by gcloud and should be removed.
```

Glob characters (such as asterisk) can be used, and support up to 10 routing rules.

Deploy the dispatch file is simple:

```sh
$ gcloud preview app deploy dispatch.yml
```

And it should require almost no time to wait. Once it is ready, the requests sent to `admin.example.com` will be routed properly to the `admin` module.

<!-- more -->

Here is the [gcloud] versions tested:

```sh
$ gcloud version
Google Cloud SDK 0.9.78

app 2015.09.11
bq 2.0.18
bq-nix 2.0.18
core 2015.09.11
core-nix 2015.09.03
gcloud 2015.09.11
gsutil 4.15
gsutil-nix 4.14
preview 2015.09.03
```

[1]: https://cloud.google.com/appengine/docs/go/modules/
[2]: https://cloud.google.com/appengine/docs/go/modules/routing
[managed-vms]: https://cloud.google.com/appengine/docs/managed-vms/
[Google Cloud Platform]: https://cloud.google.com/
[gcloud]: https://cloud.google.com/sdk/
[module-hierarchy]: /2015/09/24/add-a-custom-domain-to-the-specific-module-of-google-app-engine-managed-vms/modules-hierarchy.png "Source: Google App Engine Module Hierarchy"
