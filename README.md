LXD Container Ansible Role
=========

This role manages LXD/LXC Containers on a remote Linux Container Host.  https://www.ubuntu.com/containers/lxd

[![Build Status](https://travis-ci.org/ahnooie/ansible-role-lxd-container.svg?branch=master)](https://travis-ci.org/ahnooie/ansible-role-lxd-container)

Requirements
------------

* LXD 2.0 or greater must be installed on the LXD host and ansible server (it should already be installed by default on Ubuntu 16.04)

* LXD should already be setup on the remote host using `sudo lxd init` or using an Ansible Role such as [juju4/lxd](https://galaxy.ansible.com/juju4/lxd/)

* In order for Ansible to manage an LXD host remotely the following commands must be run ahead of time:

On the remote LXD host:

```
$ lxc config set core.https_address [::]:8443
$ lxc config set core.trust_password replace-this-with-a-secure-password
```

On the Ansible host:

```
$ lxc config set core.https_address [::]:8443
```

```
$ lxc remote add lxd4 lxd4.example.com
```
(replace lxd4.example.com with the hostname of your LXD host, 'lxd4' can be named whatever you want, you'll need to reference it in the inventory file)

* Tested on an LXD host and Ansible host both using Ubuntu 16.04 LTS (may work with other distros)

Role Variables
--------------

These variables are documented here: http://docs.ansible.com/ansible/latest/lxd_container_module.html

* `state:` started (default), stopped, restarted, absent, frozen
* `type:` image (default)
* `mode:` pull (default)
* `server:` https://images.linuxcontainers.org (default)
* `protocol:` lxd (default)
* `alias:` ubuntu/xenial/amd64 (default)
* `wait_for_ipv4_addresses:` true (default)
* `timeout:` 600 (default)

Additional Variables:
* `public_key:` "{{ lookup('file','~/.ssh/id_rsa.pub') }}" (default) - path to public ssh key to install in container
* `enable_ssh:` true (default) - installs and enables the openssh server in the container.
* `lxd_host:` your lxd container host



Dependencies
------------

None


Install
-------

```
$ ansible-galaxy install ahnooie.lxd-container
```

Example
----------------

The following example will install 6 containers with various Linux distributions on the lxd4.example.com LXD host; and on each host install python, add a public ssh key for the root user, install and start the sshd service.

### Inventory File Example


```
# Remote LXD Host
[lxd]
lxd4.example.com ansible_user=root

# Containers on LXD Hosts
[linux-containers]
ubuntu01.example.com ansible_host=lxd4:ubuntu01 alias=ubuntu/xenial/amd64
centos01.example.com ansible_host=lxd4:centos01 alias=centos/7/amd64
centos02.example.com ansible_host=lxd4:centos02 alias=centos/6/amd64
debian01.example.com ansible_host=lxd4:debian01 alias=debian/stretch/amd64
fedora01.example.com ansible_host=lxd4:fedora01 alias=fedora/27/amd64

[linux-containers:vars]
ansible_connection=lxd
lxd_host=lxd4.example.com
```
### Playbook Example containers.yml

```
---
- hosts: linux-containers
  gather_facts: false
  vars:
    public_key: "{{ lookup('file','public_keys/id_rsa.pub') }}"
  roles:
  - ahnooie.lxd-container
```

### Playbook Command Example

```
$ ansible-playbook -i inventory containers.yml

```

License
-------

MIT

Author Information
------------------

Created by [Benjamin Bryan](https://b3n.org)
