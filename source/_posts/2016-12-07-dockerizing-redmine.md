title: Dockerizing Redmine
date: 2016-12-07 12:00:00
categories: [DevOps]
tags: [redmine, docker, docker-compose, mysql]
---

Migrate an existing [Redmine] application into a [Docker] based container. There are two main steps:

1. Migrate MySQL database
2. Migrate Redmine application


# MySQL

Create an archive from the source data directory, where data to be migrated:

```sh
$ sudo tar -C /var/lib/mysql -czf ~/mysql.tar.gz .
```

The directory `/var/lib/mysql` is the default location where all database related data are stored.

Download the archive:

```sh
$ scp example.com:~/mysql.tar.gz .
```

Extract to the directory where we will map the host volume to the guest:

```sh
$ sudo mkdir -p /srv/mysql && sudo tar -C $_ -xzf mysql.tar.gz 
```

Download the latest (`5.7.16` **make sure it is the latest, because `8.x` branch might not work**) [MySQL Docker image]:

```sh
$ docker pull mysql && docker pull mysql:5.7.16
```

Run a MySQL container with the data directory mounted from the host:

```sh
$ docker run                         \
  --detach                           \
  --name mysql                       \
  --restart always                   \
  --volume /srv/mysql:/var/lib/mysql \
  mysql
```

Environment variables (`MYSQL_ROOT_PASSWORD`, `MYSQL_DATABASE`, `MYSQL_USER`, or `MYSQL_PASSWORD`) become unnecessary:

> Do note that none of the variables below will have any effect if you start the container with a data directory that already contains a database: any pre-existing database will always be left untouched on container startup.[^1]

Now I should be able to access my existing database:

```plain
$ docker exec -it mysql sh -c 'mysql -uredmine -p redmine'
Enter password:
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.7.16 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

But when accessing MySQL database from another host, we have to update the permissions. Let's log into MySQL with `root` user:

```sh
$ docker exec -it mysql sh -c 'mysql -uroot -p mysql'
```

Issue SQL commands:

```sql
mysql> SELECT Host, User FROM db WHERE Db='redmine';
+-----------+---------+
| Host      | User    |
+-----------+---------+
| localhost | redmine |
+-----------+---------+
1 row in set (0.00 sec)

mysql> SELECT Host, User FROM user WHERE User='redmine';
+-----------+---------+
| host      | user    |
+-----------+---------+
| localhost | redmine |
+-----------+---------+
1 row in set (0.00 sec)
```

Only `localhost` is allowed for both the database and the user. This is problematic. How does one know the host of the container yet to be created? The easiest way is to allow access from all hosts:

```sql
mysql> UPDATE db SET Host='%' WHERE Db='redmine';
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> UPDATE user SET Host='%' WHERE User='redmine';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

We can restrict to a set of IP addresses such as `172.17.0.%` or sub domains `%.example.com`. Docker networking uses `172.17.0.x` range.

The better way is that when linking containers, should be able to update `mysql` container `/etc/hosts` file to include a `redmine` container field. Then, a wild card is not necessary.

But having to allow all hosts to connect is fine, because all hosts do not mean *all hosts*. The port is not bind or map to any open port in the host machine. Only the host (`172.17.0.1`) and other Docker containers inside the host can discover the service provided. Other machines or containers outside the host is not able to access at all.

Restart the container to have the update taking effect:

```sh
$ docker restart mysql
```


# Redmine

For a basic Redmine setup, there aren't much to migrate, mainly just three directories, `config`, `files`, and `log`, others are application data. Configuration could be done via environment variables, and if logs weren't important to migrate, we just need to move the `files` directory, which is used for file upload.

<!-- more -->

Create an archive from the source data directory:

```sh
$ sudo tar -C /opt/redmine -czf ~/redmine.tar.gz .
```

Download the archive:

```sh
$ scp example.com:~/redmine.tar.gz .
```

Extract the just the `files` directory and copy to the new location:

```sh
$ sudo mkdir -p /srv/redmine && sudo cp -R files /srv/redmine/files
```

Configuration directory (`config`) contains only `database.yml` file, which can be overridden via environment variables. One less volume to map. And here is the mapping from the file to the environment variables:

```
$ cat config/database.yml
production:
  adapter:  mysql2
  host:     mysql   # REDMINE_DB_MYSQL
  database: redmine # REDMINE_DB_DATABASE
  username: redmine # REDMINE_DB_USERNAME
  password: redmine # REDMINE_DB_PASSWORD
  encoding: utf8
```

Download the latest (`3.3.1`) [Redmine Docker image]:

```sh
$ docker pull redmine && docker pull redmine:3.3.1
```

Run Redmine container with link to the MySQL container:

```sh
$ docker run                                         \
  --detach                                           \
  --name redmine                                     \
  --restart always                                   \
  --publish 8080:3000                                \
  --link mysql:mysql                                 \
  --env REDMINE_DB_MYSQL=mysql                       \
  --env REDMINE_DB_USERNAME=redmine                  \
  --env REDMINE_DB_PASSWORD=redmine                  \
  --env REDMINE_DB_DATABASE=redmine                  \
  --volume /srv/redmine/files:/usr/src/redmine/files \
  --volume /srv/redmine/log:/usr/src/redmine/log     \
  redmine
```

We still want to map the `log` directory even we are not migrating the old logs, because we still need to access the new logs without dropping into the container.

Containers are linked:

```
$ docker exec -it redmine sh -c 'cat /etc/hosts'
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      mysql mysql
172.17.0.3      redmine
```

If using [Docker Compose]:

```yml
version: '2'

services:
  mysql:
    image: mysql
    restart: always
    hostname: mysql
    container_name: mysql
    network_mode: bridge
    volumes:
      - /srv/mysql:/var/lib/mysql
  redmine:
    image: redmine
    restart: always
    hostname: redmine
    container_name: redmine
    network_mode: bridge
    ports:
      - '8080:3000'
    volumes:
      - /srv/redmine/files:/usr/src/redmine/files
      - /srv/redmine/log:/usr/src/redmine/log
    environment:
      - REDMINE_DB_MYSQL=mysql
      - REDMINE_DB_USERNAME=redmine
      - REDMINE_DB_PASSWORD=redmine
      - REDMINE_DB_DATABASE=redmine
    links:
      - mysql:mysql
```


# Settings

- Docker v1.12.3
- MySQL v5.7.16
- Redmine v3.3.1
- Docker Compose v1.9.0


[^1]: [mysql](https://hub.docker.com/_/mysql/), Docker Hub
[MySQL 5.7 Dockerfile]: https://github.com/docker-library/mysql/blob/3e89b55110565908b46ed3e1b1cae6098f464965/5.7/Dockerfile
[MySQL Docker image]: https://hub.docker.com/_/mysql/
[Redmine]: https://redmine.org/
[Docker]: https://www.docker.com/
[Redmine Docker image]: https://hub.docker.com/_/redmine/
[Docker Compose]: https://docs.docker.com/compose/
