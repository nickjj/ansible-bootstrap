## What is ansible-bootstrap? [![Build Status](https://secure.travis-ci.org/nickjj/ansible-bootstrap.png)](http://travis-ci.org/nickjj/ansible-bootstrap)

It is an [Ansible](http://www.ansible.com/home) role to:

- Configure a server so that you can run Ansible against it
- Install essential packages such as, `dbus`, `lsb-release` and `sudo`

## Why would you want to use this role?

Ansible requires that you install Python on your server before you can run it.
This role uses raw commands to configure your server through Ansible without
depending on any Ansible modules.

## Supported platforms

- Ubuntu 16.04 LTS (Xenial)
- Ubuntu 18.04 LTS (Bionic)
- Debian 8 (Jessie)
- Debian 9 (Stretch)

## Role variables

```
# The Python binary name to test whether or not Python is installed.
bootstrap_python_binary: "python"

# The Python package name to install with apt. It defaults to Python 2.7 as is.
bootstrap_python_package: "python"

# Packages that will get installed after Ansible is bootstrapped.
bootstrap_essential_packages:
  - "dbus"
  - "lsb-release"
  - "sudo"

# How long should the apt-cache last in seconds?
bootstrap_apt_cache_time: 86400
```

## Example usage

It's typically a good idea to create a separate `bootstrap.yml` playbook that does
nothing except bootstrap Ansible. That's because you'll need to run Ansible
with `gather_facts: False` for the bootstrap play.

Create a `bootstrap.yml` file and add this to it:

```
---

- name: Bootstrap all servers
  hosts: "all"
  gather_facts: False
  become: True

  roles:
    - { role: "nickjj.bootstrap", tags: ["bootstrap"] }
```

Let's say you want to edit the essential package list, you can do this by
opening or creating `group_vars/all.yml` which is located relative to your
`inventory` directory and then make it look something like this:

```
---

# Personally I wouldn't add htop as an essential package (it should be a role),
# but this is an example of how to modify the list of packages.
bootstrap_essential_packages:
  - "dbus"
  - "htop"
  - "lsb-release"
  - "sudo"
```

Now you would run `ansible-playbook -i inventory/hosts bootstrap.yml`.

You can also chain this playbook together with other playbooks by running
`ansible-playbook -i inventory/hosts bootstrap.yml site.yml`. That could be
useful to do on your first Ansible run on a server.

## Installation

`$ ansible-galaxy install nickjj.bootstrap`

## Ansible Galaxy

You can find it on the official
[Ansible Galaxy](https://galaxy.ansible.com/nickjj/bootstrap/) if you want to
rate it.

## License

MIT
