ansible-ssh-users
=========
[![Build Status](https://travis-ci.org/1nfinitum/ansible-users.svg?branch=master)](https://travis-ci.org/1nfinitum/ansible-users)

Role for managing users, groups, authorized ssh keys and basic SSH security configs.

Requirements
------------
This role requires only root access for accomplishing its operations, so either run it in a playbook with a global `become: yes`, or invoke the role in your playbook like:
```
- hosts: localhost
  roles:
    - role: 1nfinitum.ssh-users
```

Role Variables
--------------

Available variables are listed below, along with default values (see defaults/main.yml):
```
default_users_group: {name: users} 
default_user_shell: /bin/bash
```
Here is defined default group in which would be added users if you wouldn't define `group` parameter for user and default Shell for users.

```
users: []
authorized_keys: []
```
These variables encapsulate all the [user](http://docs.ansible.com/ansible/user_module.html) and [authorized_key](http://docs.ansible.com/ansible/authorized_key_module.html) Ansible Modules functionality and adds additional parameter:
```
ssh_pub_key: []
```
This variable takes a list of public SSH keys as a string or as a an url like `https://github.com/username.keys` and adds it to user's `authorized_keys` file related to its account. Note that while `authorized_keys` parameter you should put SSH keys also into `ssh_pub_key` list. For example:
```
users:
  - user: abc
    password: $6$rounds=656000$49G.PQKkbGk2ngY1$BUDsC3aQf3XXPpsU8.JPP94URmiYF21fbYdxU/K8G0iJstvc3EEVmrFW5K51b7q4J.qgliRV16O5tpMSjuXhY1
    shell: /bin/bash
    createhome: yes
    state: present
    ssh_pub_key:
      - "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDN43dn7fgS1LH4dCJt3NK2DIG82rYelEPGOcG8xU2WkRL9LjCZb42JIi1fSbvHPIgZXxWc2w01h2fYVBwyHXDU+7mLNMc3ZpcKCVW3hoAb7AVA/WwaBgrtBLmuU1M2eM+d//ih5kDnAS58ZmcUg8JYxvqc4tJyFQh969lGQ+UQab/BPVvoAP9ntPSk89qOYrm04l1uIxVayQT+taYXG37akp4nAaGsGF4Si/kRVzhjAgP/VuJvq4y3STUIIi4pmjhSQX4fyULQNY58aaYW4AXoGFb2S6xKX4oxCRuyhFaJdPtNCTGGyYTISkrevpSWlZSbdYOxijLZTaFNg7h+ngIV mykhaylokolesnik@Air-Mykhaylo"

authorized_keys:
  - user: abc # Name of user to assign key for. Required.
    state: present # Default is present.
    ssh_pub_key:
      - key: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDN43dn7fgS1LH4dCJt3NK2DIG82rYelEPGOcG8xU2WkRL9LjCZb42JIi1fSbvHPIgZXxWc2w01h2fYVBwyHXDU+7mLNMc3ZpcKCVW3hoAb7AVA/WwaBgrtBLmuU1M2eM+d//ih5kDnAS58ZmcUg8JYxvqc4tJyFQh969lGQ+UQab/BPVvoAP9ntPSk89qOYrm04l1uIxVayQT+taYXG37akp4nAaGsGF4Si/kRVzhjAgP/VuJvq4y3STUIIi4pmjhSQX4fyULQNY58aaYW4AXoGFb2S6xKX4oxCRuyhFaJdPtNCTGGyYTISkrevpSWlZSbdYOxijLZTaFNg7h+ngIV mykhaylokolesnik@Air-Mykhaylo" # SSH public key as a string or as url.
```
`groups_to_add` variable encapsulates [group](http://docs.ansible.com/ansible/group_module.html) Ansible Module functionality, example usage:
```
groups_to_add:
  - name: abc # Name of group. Required.
    state: absent # Default is 'present'
```
To let some user be in `sudoers` file (meaning it will be able to accomplish `root` operations) next two variables, where you should define preferred users, can be used:
```
passwordless_sudoers: [] # To define users, that will be able to accomplish `root` operations without obligation to write `sudo` before commands.
passworded_sudoers: [] # To define users, that can obtain `root` operations using `sudo` command and their password.
```
NOTE that users without defined password cannot obtain `sudo` operations.

The following variables are obtaining basic SSH security configurations to keep your server safer. By default these configurations is turned `off`, to prevent critical changes for existing servers, so if you want to use it - set `enable_ssh_config` to `true` in your `vars` or vars file you use.
```
ssh_port: 22
```
The port through which you'd like SSH to be accessible. The default is port 22, but if you're operating a server on the open internet, and have no firewall blocking access to port 22, you'll quickly find that thousands of login attempts per day are not uncommon. You can change the port to a nonstandard port (e.g. 2849) if you want to avoid these thousands of automated penetration attempts.
```
ssh_password_authentication: "no"
ssh_permit_root_login: "no"
ssh_use_dns: "no"
```
Security settings for SSH authentication. It's best to leave these set to "no", but there are times (especially during initial server configuration or when you don't have key-based authentication in place) when one or all may be safely set to 'yes'.
```
fail2ban_enabled: false
```
Wether to install/enable fail2ban. By default it's turned off, to enable set it to `true`, note that it will work only on Debian type machine, while it uses `apt` package for installation. You might not want to use fail2ban if you're already using some other service for login and intrusion detection.

Example Playbook
----------------
```
- hosts: localhost
  become: yes
  vars_files:
    - vars/main.yml
  roles:
    - { role: 1nfinitum.ssh-users }
```
Inside `vars/main.yml`:
```
users:
  - user: abc
    password: $6$rounds=656000$49G.PQKkbGk2ngY1$BUDsC3aQf3XXPpsU8.JPP94URmiYF21fbYdxU/K8G0iJstvc3EEVmrFW5K51b7q4J.qgliRV16O5tpMSjuXhY1
    shell: /bin/bash
    createhome: yes
    state: present
    ssh_pub_key:
      - "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDN43dn7fgS1LH4dCJt3NK2DIG82rYelEPGOcG8xU2WkRL9LjCZb42JIi1fSbvHPIgZXxWc2w01h2fYVBwyHXDU+7mLNMc3ZpcKCVW3hoAb7AVA/WwaBgrtBLmuU1M2eM+d//ih5kDnAS58ZmcUg8JYxvqc4tJyFQh969lGQ+UQab/BPVvoAP9ntPSk89qOYrm04l1uIxVayQT+taYXG37akp4nAaGsGF4Si/kRVzhjAgP/VuJvq4y3STUIIi4pmjhSQX4fyULQNY58aaYW4AXoGFb2S6xKX4oxCRuyhFaJdPtNCTGGyYTISkrevpSWlZSbdYOxijLZTaFNg7h+ngIV mykhaylokolesnik@Air-Mykhaylo"

groups_to_add:
  - name: abc # Name of group. Required.
    state: absent # Default is 'present'
    
authorized_keys:
  - user: abc # Name of user to assign key for. Required.
    state: present # Default is present.
    ssh_pub_key:
      - key: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDN43dn7fgS1LH4dCJt3NK2DIG82rYelEPGOcG8xU2WkRL9LjCZb42JIi1fSbvHPIgZXxWc2w01h2fYVBwyHXDU+7mLNMc3ZpcKCVW3hoAb7AVA/WwaBgrtBLmuU1M2eM+d//ih5kDnAS58ZmcUg8JYxvqc4tJyFQh969lGQ+UQab/BPVvoAP9ntPSk89qOYrm04l1uIxVayQT+taYXG37akp4nAaGsGF4Si/kRVzhjAgP/VuJvq4y3STUIIi4pmjhSQX4fyULQNY58aaYW4AXoGFb2S6xKX4oxCRuyhFaJdPtNCTGGyYTISkrevpSWlZSbdYOxijLZTaFNg7h+ngIV mykhaylokolesnik@Air-Mykhaylo" # SSH public key as a string or as url.
```

License
-------

MIT

Author Information
------------------

This role was created in 2017 by [Mykhaylo Kolesnik](http://github.com/1nfinitum).
