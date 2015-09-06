title: Upgrade Vagrant in Windows from Command Line without Prompt
date: 2015-09-06 12:00:00
categories: [DevOps]
tags: [upgrade, install, vagrant, command-line, cli, windows, msiexec, bintray]
---

To determine whether your [Vagrant] is out of date or not, issue:

```sh
$ vagrant version
Installed Version: 1.7.4
Latest Version: 1.7.4

You're running an up-to-date version of Vagrant!
```

If yours is out of date, you can download it from its [download] page. However, being a lazy programmer, that's still too many clicks. I want to figure out a way to update or install Vagrant from the command line without prompt.

And here you go!

Vagrant binaries are distributed by [Bintray], its format is:

```plain
https://dl.bintray.com/mitchellh/vagrant/vagrant_{version}.msi
```

For example:

```plain
https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.4.msi
```

After you issue `vagrant version`, you know the correct version to upgrade, then download it:

```sh
$ curl -sSL https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.4.msi > vagrant.msi
```

Install it by `msiexec`:

```sh
$ msiexec /i C:\Users\Chao\Downloads\vagrant.msi /passive /norestart
```

Make sure to use the full path instead of relative `.\vagrant.msi`, and Vagrant should be installed in the default location `C:\HashiCorp\Vagrant`. To find out all possible options, Just type `msiexec` command only.

Saving a few clicks!

[Vagrant]: https://www.vagrantup.com/
[Bintray]: https://bintray.com/
[download]: https://www.vagrantup.com/downloads.html
