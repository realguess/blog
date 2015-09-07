title: Setup Development Environment with Vagrant on Google Compute Engine
date: 2015-09-07 12:00:00
categories: [DevOps]
tags: [setup, install, google-compute-engine, gce, vagrant, vagrant-plugin, vagrant-box, vagrant-google]
---

We usually use [Vagrant] to provision and manage [VirtualBox] virtual machines or VMs. And Vagrant only ships with VirtualBox support by default. But Vagrant can do much more. VirtualBox is just one of [providers], additional providers can be added via the [Vagrant plugin system][plugins]. Here, we are going to use [Google Compute Engine] as the provider and setup a disposable development environment.

Basic steps involve:

1. Install Vagrant
2. Install vagrant-google plugin
3. Add SSH keys to metadata server
4. Add Vagrant box
5. Add a Vagrantfile and override the defaults from the box
6. Provision the machine

First, install Vagrant (exampled by [Debian] systems such as [Ubuntu]):

```sh
$ file=/tmp/vagrant.deb version=1.7.4 && \
  curl -sSL https://dl.bintray.com/mitchellh/vagrant/vagrant_${version}_x86_64.deb > ${file} && \
  sudo dpkg -i ${file} && \
  rm ${file} && \
  vagrant version
```

Install dependencies for building Debian packages, which are needed for the plugin:

```sh
$ sudo apt-get install -y build-essential
```

Install [vagrant-google] plugin:

```sh
$ vagrant plugin install vagrant-google
Installing the 'vagrant-google' plugin. This can take a few minutes...
Installed the plugin 'vagrant-google (0.2.1)'!
```

In order to SSH into the VM, we need to add public key to the instance which will be provisioned. In Google Compute Engine, this is done via [metadata] server at the project level.

Add the username to prefix the SSH key, so the Compute Engine will pick it up and create the corresponding user:

```sh
$ echo -n 'chao:' > /tmp/id_rsa.pub
$ cat ~/.ssh/id_rsa.pub >> /tmp/id_rsa.pub
```

Now the concatenated one will have the username:

```text
chao:ssh-rsa AAAAB3... chao@ubuntu
```

Setting up SSH keys at the project level:

```sh
$ gcloud compute project-info add-metadata --metadata-from-file \
  sshKeys=/tmp/id_rsa.pub
```

[Google Cloud SDK] or `gcloud` command should be installed already. Now it's time to configure the machine to be provisioned.

The plugin provides a Vagrant [box], which is a package format for Vagrant environments. It is not necessary to create a new box, we can just override the defaults by the `Vagrantfile`. So add the provider-specific box:

```sh
$ vagrant box add gce https://github.com/mitchellh/vagrant-google/raw/master/google.box
```

Make sure the box is added:

```sh
$ vagrant box list
```

Add a `Vagrantfile` and override the defaults:

<!-- more -->

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "gce"

  config.vm.provider :google do |google, override|
    google.google_project_id = "PROJECT_ID"
    google.google_client_email = "PROJECT_ID_SERVICE_ACCOUNT@developer.gserviceaccount.com"
    google.google_json_key_location = "~/path/to/gcloud.json"

    # Define the name of the instance.
    google.name = "devel"

    # Set the zone where the instance will be located. To find out available zones:
    # `gcloud compute zones list`.
    google.zone = "asia-east1-c"

    # Set the machine type to use. To find out available types:
    # `gcloud compute machine-types list --zone asia-east1-c`.
    google.machine_type = "n1-standard-2"

    # Set the machine image to use. To find out available images:
    # `$ gcloud compute images list`.
    google.image = "ubuntu-1404-trusty-v20150901a"

    override.ssh.username = "chao"
    override.ssh.private_key_path = "~/.ssh/id_rsa"
  end
end
```

Above just lists frequently used ones, there are more configuration options, see:

 - <https://github.com/mitchellh/vagrant-google#configuration>

Provision the machine:

```sh
$ vagrant up --provider=google
Bringing machine 'default' up with 'google' provider...
==> default: Launching an instance with the following settings...
==> default:  -- Name:            devel
==> default:  -- Type:            n1-standard-2
==> default:  -- Disk type:       pd-standard
==> default:  -- Disk size:       10 GB
==> default:  -- Disk name:
==> default:  -- Image:           ubuntu-1404-trusty-v20150901a
==> default:  -- Zone:            asia-east1-c
==> default:  -- Network:         default
==> default:  -- Metadata:        '{}'
==> default:  -- Tags:            '[]'
==> default:  -- IP Forward:
==> default:  -- External IP:
==> default:  -- Preemptible:     false
==> default:  -- Auto Restart:    true
==> default:  -- On Maintenance:  MIGRATE
==> default:  -- Autodelete Disk: true
==> default:  -- Scopes:
==> default: Waiting for instance to become "ready"...
==> default: Machine is booted and ready for use!
==> default: Waiting for SSH to become available...
==> default: Machine is ready for SSH access!
==> default: Rsyncing folder: /home/chao/vagrant/ => /vagrant
```

SSH into the machine:

```sh
$ vagrant ssh
```

Dispose the machine:

```sh
$ vagrant destroy
```

Now it's time to customize the machine!


[Vagrant]: https://www.vagrantup.com/
[VirtualBox]: https://www.virtualbox.org/
[Google Compute Engine]: https://cloud.google.com/compute/
[providers]: https://docs.vagrantup.com/v2/providers/index.html
[plugins]: https://docs.vagrantup.com/v2/plugins/index.html
[vagrant-google]: https://github.com/mitchellh/vagrant-google
[Debian]: https://www.debian.org/
[Ubuntu]: http://www.ubuntu.com/
[metadata]: https://cloud.google.com/compute/docs/metadata
[Google Cloud SDK]: https://cloud.google.com/sdk/
[box]: https://docs.vagrantup.com/v2/boxes.html
