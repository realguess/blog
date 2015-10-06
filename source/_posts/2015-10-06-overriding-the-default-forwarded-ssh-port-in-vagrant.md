title: Overriding the Default Forwarded SSH Port in Vagrant
date: 2015-10-06 12:00:00
categories: [DevOps]
tags: [port-forwarding, ssh, vagrant, virtualbox]
---

TLDR:

```ruby
# Must specified `id: "ssh"` in order to override the default forwarded SSH port.
config.vm.network :forwarded_port, guest: 22, host: 2322, id: "ssh"
```

What's the problem? If running multiple virtual machines managed by Vagrant, SSH port collision will happen:

```plain
Vagrant cannot forward the specified ports on this VM, since they
would collide with some other application that is already listening
on these ports. The forwarded port to 2222 is already in use
on the host machine.

To fix this, modify your current projects Vagrantfile to use another
port. Example, where '1234' would be replaced by a unique host port:

  config.vm.network :forwarded_port, guest: 22, host: 1234

Sometimes, Vagrant will attempt to auto-correct this for you. In this
case, Vagrant was unable to. This is usually because the guest machine
is in a state which doesn't allow modifying port forwarding.
```

The example is _correct_ but _incomplete_. By default the SSH port (`22`) on the guest machine is forwarded to port `2222` on the host machine:

```ruby
# Source: https://github.com/mitchellh/vagrant/blob/v1.7.2/plugins/kernel_v2/config/vm.rb
if !@__networks["forwarded_port-ssh"]
  network :forwarded_port,
    guest: 22,
    host: 2222,
    host_ip: "127.0.0.1",
    id: "ssh",
    auto_correct: true
end
```

Changing to another port in `Vagrantfile` does not solve the problem. For example, switching from `2222` to `2322`:

```ruby
# Create a forwarded port mapping which allows access to a specific port
# within the machine from a port on the host machine. In the example below,
# accessing "localhost:8080" will access port 80 on the guest machine.
config.vm.network "forwarded_port", guest: 22, host: 2322
```

In this situation, you are forwarding both ports `2222` and `2322` to the SSH port on the guest machine, as shown in [VirtualBox] port forwarding settings:

![Port Forwarding Rules](/2015/10/06/overriding-the-default-forwarded-ssh-port-in-vagrant/port-forwarding-rules.jpg)


When bringing up the machine, there will be multiple host machine ports forwarding to the same SSH port.

```sh
$ vagrant up
==> default: Forwarding ports...
    default: 22 => 2322 (adapter 1)
    default: 22 => 2222 (adapter 1)
```

The SSH configuration shows the same outcome:

```sh
$ vagrant ssh-config
Host default
  HostName 127.0.0.1
  User chao
  Port 2222
```

Of course, you can do:

```sh
$ ssh -p 2322 chao@localhost
```

This works, but we like the simple `vagrant ssh` command, and we just want to retain a single port `2322`.

The solution is appending `id: "ssh"`, and it must be specified in order to override the default forwarded SSH port. See < https://github.com/mitchellh/vagrant/issues/3232#issuecomment-39354659>:

```ruby
# Must specified `id: "ssh"` in order to override the default forwarded SSH port.
config.vm.network :forwarded_port, guest: 22, host: 2322, id: "ssh"
```

After appending `id: "ssh"`, the SSH configuration has also been updated:

```sh
$ vagrant ssh-config
Host default
  HostName 127.0.0.1
  User chao
  Port 2322
```

Now, you are good to go:

```sh
$ vagrant ssh
```


[Vagrant]: https://www.vagrantup.com/
[VirtualBox]: https://www.virtualbox.org/
