title: Resetting GitLab User Password with a Simple Shell Script
date: 2016-07-02 12:00:00
category: [DevOps]
tags: [password, gitlab, docker]
---


## Problem

Resetting password is one of the most common requests virtually in any system. In [GitLab], user password can be updated by visiting the `/admin/users` page. But if you forgot the password for the root user or the admin user. You need another method to reset it.


## Objective


The goal is to simplify the process of resetting GitLab user password by using CLI, so next time when encountering the same problem again, it will be quick and easy.


## Settings

Self-hosted [GitLab], installed in a [Docker] container.


## Solutions

Step by step procedure to reset the root password is already provided by [this GitLab documentation][1]. By converting it into a Bash shell script and placing it in user's home `bin` directory as an executable `~/bin/gitlab-password-reset` file, we will have created a simple command to be run repeatedly:

```
#!/usr/bin/env bash

echo -n "Email: ";    read MAIL
echo -n "Password: "; read -s PASS
echo # Ensure a new line

docker exec gitlab gitlab-rails runner -e production "  \
  user = User.find_by(email: '$MAIL');                  \
  user.password = user.password_confirmation = '$PASS'; \
  user.save!"
```

We could simply run the long `docker` command instead of shell script. But since we're dealing with password, it's a good practice to avoid placing sensitive information on command line history log.

No trailing spaces are allowed on the password field, by the way.

Another solution is turning the Ruby evaluation into a script and save into somewhere like `/srv/gitlab/config` directory. Then, we can just run:

```
$ docker exec gitlab gitlab-rails runner -e production /etc/gitlab/scripts/password-reset.rb
```

Because we are using Docker to run GitLab, and the following directories are mapped from the host to the guest:

```
/srv/gitlab/config:/etc/gitlab
/srv/gitlab/logs:/var/log/gitlab
/srv/gitlab/data:/var/opt/gitlab
```

Therefore, when executing the Ruby script, it's `/etc/gitlab` instead of `/srv/gitlab`. However, you will need to figure out how to get the email and password into the script. That's for you to answer.


[1]: http://docs.gitlab.com/ce/security/reset_root_password.html
[Docker]: https://www.docker.com/
[GitLab]: https://gitlab.com/
