awx_debian
==========

The awx_debian role provisions a "standalone" AWX instance
Tested on Debian 9 and 10 + Ansible >= 2.5
If you do not have Ansible >= 2.5 try out the [Installer for AWX Debian](https://github.com/djangoxv/install_awx_debian)

This role is useful for non-container environments that rely on Debian builds

It is a quick way to install and start working with AWX, using a localhost
for Postgres, Memcached, and RabbitMQ


#### Sample Playbook
```
---

- name: Provision AWX on a Debian Machine
  hosts: awxdeb
  vars:
   awx_debian_admin: 'admin'
   awx_debian_pw:    'AnAWXer'
   awx_debian_secret_key: 'P9HunLc9uq9ClFclpKOj' 
  roles:
    - awx_debian
```

#### Plays in Order of Occurrence
- prereqs.yml    Checks that target inventory is Debian 9 or 10 and ansible is >= 2.5
- deps.yml       Provisions AWX dependent services: Memcached, Postgres, RabbitMQ, and Nginx
- nginx.yml      Installs & Configures nginx Reverse Proxy for AWX
- postgres.yml   Installs a local AWX database and applies permissions/roles
- install.yml    Downloads, builds, and installs AWX + requirements
- config.yml     Django migrations and setup
- services.yml   Starts AWX systemd services

#### Systemd Services in Order of Startup
- awx-django     The web interface
- awx-daphne     Django Channels
- awx-dispatch   Task Queue orchestration and scheduling 
- awx-callback   Callback Receiver
- awx-workers    Task channel workers

#### Extra Variables
|          Name            |    Options      |  Default  |         Purpose                   |
|------------------------- | --------------- | --------- | ----------------------------------|
| awx_debian_refresh       |   True/False    |   False   | Remove AWX and start again        |
| awx_debian_nginx_ssl     |   True/False    |   True    | Creates a Self-Signed Certificate |
| awx_debian_official_logo |   True/False    |   False   | Builds UI with Official awx-logos |

#### TO DO
* Not so Alone: Add External Database / RabbitMQ Support
* BYOC:         Allow for SSL certificate imports
* Superuser:    Must awx_debian_refresh=True to redo new administrator
