Ansible Role: Users
=========
[![Build Status](https://travis-ci.org/tenequm/ansible-ssh-users.svg?branch=master)](https://travis-ci.org/tenequm/ansible-ssh-users)

Role for managing users, groups and authorized ssh keys.

Requirements
------------
This role requires only root access for accomplishing its operations, so either run it in a playbook with a global `become: yes`, or invoke the role in your playbook like:
```
    - hosts: localhost
      become: yes
      roles:
        - role: tenequm.users
```
Role Variables
--------------
Variable to add users:
```
users_add: [] # List
```
It has a form of list and encapsulates all the [user](http://docs.ansible.com/ansible/user_module.html) Ansible Module functionality. If the group mentioned in `group` parameter doesn't exist - it will create it before adding user. When adding groups to `groups` parameter be sure that those groups exist, if not - use `users_add_group` variable to add them in the same play as creating user, it will be explained below.
If you are going to add password, use this [instruction](http://docs.ansible.com/ansible/faq.html#how-do-i-generate-crypted-passwords-for-the-user-module), cause it only takes encrypted values of the password. It also has some additional parameters:
```
ssh_pub_key: [] # List
sudoer: '' # String
```
`ssh_pub_key` parameter takes a list of public SSH keys as a string or as a an url like `https://github.com/username.keys` and adds it to user's `authorized_keys` file related to its account.
`sudoer` parameter lets you decide whether user to be in `sudoers` file or not(what lets you accomplish `sudo` command). It's `true` and `false` values and, by default, will add user as *Passworded sudoer*. In case you want user to be *Passwordless sudoer* set its value to `passwordless`.
```
users_add:
  - user: abc
    sudoer: passwordless # Set it to `true` to achieve passworded sudoer.
    ssh_pub_key:
      - "your SSH key" # SSH public key as a string or as url.
```
Variable for creation/removal of groups:
```
users_add_group: [] # List
```
It encapsulates all the [group](http://docs.ansible.com/ansible/group_module.html) Ansible Module functionality:
```
users_add_group:
  - name: abc # Name of group. Required.
    state: absent # Default is 'present'
```
Variable to add/remove authorized keys of user(s):
```
users_add_authorized_keys: [] # List
```
It encapsulates all the [authorized_key](http://docs.ansible.com/ansible/authorized_key_module.html) Ansible Module functionality:
```
users_add_authorized_keys:
  - user: abc # Name of user to assign key for. Required.
    state: present # Default is `present`, can be set to `absent` for removal.
    ssh_pub_key:
      - key: "your SSH key" # SSH public key as a string or as url.
```
### Defaults
```
users_default_group: users 
users_default_shell: /bin/bash
```
This is defined default group in which user would be added if you wouldn't define `group` parameter and default Shell.

Example Playbook
----------------
```
- hosts: localhost
  become: yes
  vars_files:
    - vars/main.yml
  roles:
    - tenequm.users
```
Inside `vars/main.yml`:
```
users_add:
  - user: abc
    password: $6$rounds=656000$49G.PQKkbGk2ngY1$BUDsC3aQf3XXPpsU8.JPP94URmiYF21fbYdxU/K8G0iJstvc3EEVmrFW5K51b7q4J.qgliRV16O5tpMSjuXhY1
    sudoer: true
    ssh_pub_key:
      - "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDN43dn7fgS1LH4dCJt3NK2DIG82rYelEPGOcG8xU2WkRL9LjCZb42JIi1fSbvHPIgZXxWc2w01h2fYVBwyHXDU+7mLNMc3ZpcKCVW3hoAb7AVA/WwaBgrtBLmuU1M2eM+d//ih5kDnAS58ZmcUg8JYxvqc4tJyFQh969lGQ+UQab/BPVvoAP9ntPSk89qOYrm04l1uIxVayQT+taYXG37akp4nAaGsGF4Si/kRVzhjAgP/VuJvq4y3STUIIi4pmjhSQX4fyULQNY58aaYW4AXoGFb2S6xKX4oxCRuyhFaJdPtNCTGGyYTISkrevpSWlZSbdYOxijLZTaFNg7h+ngIV mykhaylokolesnik@Air-Mykhaylo"
      -
users_add_authorized_keys:
  - user: abc # Name of user to assign key for. Required.
    state: present # Default is present.
    ssh_pub_key:
      - key: "your public SSH key" # SSH public key as a string or as url.
```
License
-------

MIT

Author Information
------------------

This role was created in 2017 by [Mykhaylo Kolesnik](http://github.com/tenequm).
