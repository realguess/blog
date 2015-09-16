title: Use a Single Dockerfile
date: 2015-09-16 12:00:00
categories: [DevOps]
tags: [dockerfile, docker, coding-style]
---

I was planning to build two different [Docker] images: one for production and one for test. However, the more I had coded, the more frustrated I had become. I want something simple, and more means complexity:

1. Having multiple images means more configuration files to manage and update.
2. More things to do when working with third-party tools, for example, Google App Engine Managed VMs deployment will only recognize the application root level [Dockerfile] out of the box.
3. Steeper learning curve for new developers.

Keep our application structure simple is important. If you need multiple dockerfiles, then your application is too complex. The difference between `npm install --production` and `npm install` isn't a lot. But it will save you time and effort from managing multiple dockerfiles. There is no reason to have more than one Dockerfile. Just use a different command such as `npm test` when running tests.

[Docker]: https://docs.docker.com/
[Dockerfile]: https://docs.docker.com/reference/builder/
