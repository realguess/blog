title: Avoiding Dependencies Reinstall When Building NodeJS Docker Image
date: 2017-06-01 12:00:00
categories: [DevOps]
tags: [package.json, dependency, dockerfile, node, docker]
---

When I first started writing [Dockerfile][] for building a [NodeJS][] app, I will do:

```sh
WORKDIR /app
COPY . /app
RUN npm install
```

Sure, this is very simple, but every time a change in the source file, the entire dependency tree needs to be re-installed. The only time you need to rebuild is when `package.json` changes.

One trick is to move `package.json` elsewhere, and build the dependencies, then move back:

```sh
WORKDIR /app
ADD package.json /tmp
RUN cd /tmp && npm install
COPY . /app
RUN mv /tmp/node_modules /app/node_modules
```

However, if there are a lot of depending packages with lots of files, the last step will take a long time.

How about using symbolic link to shorten the time?

```sh
WORKDIR /app
ADD package.json /tmp
RUN cd /tmp && npm install
COPY . /app
RUN ln -s /tmp/node_modules /app/node_modules
```

Well, it works, but not every NPM package is happy about it. Some will complain about the softlink, result in errors.

So, how can we reuse caches as much as possible, and:

1. Do not need to reinstall dependencies
2. Do not use symbolic link

Here is a solution, just move down the `COPY` statement:

```sh
# Set the working directory, which creates the directory.
WORKDIR /app

# Install dependencies.
ADD package.json /tmp
RUN cd /tmp && npm install

# Move the dependency directory back to the app.
RUN mv /tmp/node_modules /app

# Copy the content into the app directory. The previously added "node_modules"
# directory will not be overridden.
COPY . /app
```

With this configuration, if `package.json` stays the same, only the last cache layer will be rebuilt when source codes change.


[Dockerfile]: https://docs.docker.com/engine/reference/builder/
[NodeJS]: https://nodejs.org/
