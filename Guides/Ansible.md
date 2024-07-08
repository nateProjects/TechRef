# Ansible Basics

This is a very basic overview.

## Install

Ubuntu -

```bash
sudo apt-add-repository ppa:ansible/ansible
sudo apt update
sudo apt install ansible
```

Mac -
(presuming Brew is installed)

``brew install ansible

Windows / Pipx / Pip -
(assuming Python Pip is installed)

```bash
pip install ansible
# or
pipx install --include-deps ansible
```

## Create hosts file

ansible_user ideally should be your username (see Create User below)

/etc/ansible/hosts
```bash
[DMZ]
hostname ansible_host=10.16.2.50 ansible_user=root

[ServerNetwork]
hostname ansible_host=172.16.100.45 ansible_user=username
```

See host list - ``ansible-inventory --list -y

## Send command to hosts

Test -

``ansible all -m ping -u root
or
``ansible DMZ -m shell -a "df -h"

## Ad Hoc Commands

This is the equivalent of SSH’ing into a server and running a command directly as sudo.

Run Ansible playbook - 

```bash
ansible-playbook playbook.yml -f 10
```

Reboot server - 

```bash
ansible atlanta -a "/sbin/reboot"
```

Create directory - 

```bash
ansible webservers -m ansible.builtin.file -a "dest=/path/to/c mode=755 owner=bob group=bob state=directory"
```

Install package - 

```bash
ansible webservers -m ansible.builtin.yum -a "name=http state=present"
```

Create user - 

```bash
ansible all -m ansible.builtin.user -a "name=bob password=<encrypted password>"
```

Start service - 

```bash
ansible webservers -m ansible.builtin.service -a "name=httpd state=started"
```

## Config Files

Ansible has three main files that you need to consider:

Host/inventory file: Contains the nodes that need to be managed

Ansible.cfg file: Located by default at /etc/ansible/ansible.cfg, it has the necessary privilege escalation options and the location of the inventory file

Main file: A playbook that has modules that perform various tasks on a host listed in an inventory or host file

## Modules

### Package management

There is a module for most popular package managers, such as YUM and APT, to enable you to install any package on a system.

```yaml
- name: install the latest version of Apache and MariaDB
  yum:
    name:
      - httpd
      - mariadb-server
    state: latest
```

This installs the Apache web server and the MariaDB SQL database.

### Git

The git module manages git checkouts of repositories to deploy files or software.

Example: Create git archive from repo

```yaml
- git:
    repo: https://github.com/ansible/ansible-examples.git
    dest: /src/ansible-examples
```

### Service

After installing a package, you need a module to start it. The service module enables you to start, stop, and reload installed packages.

```yaml
- name: Start service foo, based on running process /usr/bin/foo
  service:
    name: network
    state: started
```

### Copy

The copy module copies a file from the local or remote machine to a location on the remote machine.

```yaml
- name: Copy a new "ntp.conf file into place, backing up the original if it differs from the copied version
  copy:
    src: /tmp/ntp.conf
    dest: /etc/ntp.conf
    owner: root
    group: root
    mode: '0644'
    backup: yes
```

Module 5: File

The file module manages the file and its properties. It sets attributes of files, symlinks, or directories. It also removes files, symlinks, or directories.

```yaml
- name: Create a directory if it does not exist
  file:
    path: /etc/directory
    state: directory
    mode: '0755'
```

Module 6: Lineinfile

The lineinfile module manages lines in a text file.

It ensures a particular line is in a file or replaces an existing line, using a back-referenced regular expression if necessary.

```yaml
- name: Add a line to a file if the file does not exist
  lineinfile:
    path: /etc/resolv.conf
    line: 192.168.1.99 bla.lab.net bla
    create: yes
```

It's primarily useful when you want to change just a single line in a file.

### Archive

The archive module creates a compressed archive of one or more files. By default, it assumes the compression source exists on the target.

```yaml
- name: Compress directory /path/to/bla/ into /path/to/bla.zip
  archive:
    path: /path/to/bla
    dest: /path/to/bla.zip
```

### Command

One of the most basic but useful modules, the command module takes the command name followed by a list of arguments.

```yaml
- name: "Create a file if it does not exist"
  command: "touch /tmp/latestfile"
    args:
      creates: "/tmp/latestfile"
    register: createif
```

## Create User

& Add to Wheel Group + Copy SSH Key

usermake.yml

```yaml
- hosts: all
  become: true
  vars:
    NORMAL_USER_NAME: 'first.last'
    NORMAL_USER_PASSWORD: 'HASHPASSWORD'
  tasks:
    - name: Make sure we have a 'wheel' group
      group:
        name: wheel
        state: present
    - name: "Create a secondary, non-root user"
      user: name={{ NORMAL_USER_NAME }} 
            password={{ NORMAL_USER_PASSWORD }}  
            shell=/bin/bash
            group=wheel
    - name: Add remote authorized key to allow future passwordless logins
      authorized_key: user={{ NORMAL_USER_NAME }} key="{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
    - name: Allow 'wheel' group to have passwordless sudo
      lineinfile:
        dest: /etc/sudoers
        state: present
        regexp: '^%wheel'
        line: '%wheel ALL=(ALL) NOPASSWD: ALL'
    - name: Add sudoers users to wheel group
      user:
        name={{ NORMAL_USER_NAME }} 
        groups=wheel
        append=yes
        state=present
```

### Generate Hashed Password

```bash
apt install sshpass
openssl passwd -6 -stdin
# ansible-playbook usermake.yml -f 10 -kK
```

---

### User Setup

Set up host in /etc/ansible/hosts

eg.
```
[ServerNetwork]
name ansible_host=IP ansible_user=root
```

Copy user key from cat ~/.ssh/id_rsa.pub
	to new host over to root ~/.ssh/authorized_users
	chmod to 640 if not already

`ssh-copy-id username@HOST`

or `ansible host -m shell -a "sudo echo 'id_rsa.pub key here' > /root/.ssh/authorized_keys"`

Run - `ansible-playbook ~/code/ansible/usermake.yml -f 10`

Change hosts config to your username (ansible_user=me)

usermake.yml
```yaml
- hosts: all
  become: true
  vars:
    NORMAL_USER_NAME: 'first.last'
    NORMAL_USER_PASSWORD: 'HASH'
  tasks:
    - name: Make sure we have a 'wheel' group
      group:
        name: wheel
        state: present
    - name: "Create a secondary, non-root user"
      user: name={{ NORMAL_USER_NAME }}
            password={{ NORMAL_USER_PASSWORD }}
            shell=/bin/bash
            group=wheel
    - name: Add remote authorized key to allow future passwordless logins
      authorized_key: user={{ NORMAL_USER_NAME }} key="{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
    - name: Allow 'wheel' group to have passwordless sudo
      lineinfile:
        dest: /etc/sudoers
        state: present
        regexp: '^%wheel'
        line: '%wheel ALL=(ALL) NOPASSWD: ALL'
    - name: Add sudoers users to wheel group
      user:
        name={{ NORMAL_USER_NAME }}
        groups=wheel
        append=yes
        state=present
```

May need to `ssh-copy-id username@IP` and enter password
