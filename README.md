# unify_config
This is configuration for a VPS in order to run the website for Unify Water.
Some of this code comes from https://sysadmincasts.com/episodes/45-learning-ansible-with-vagrant-part-2-4


## Summary

 Everything will be provisioned using ansible.

 - Wordpress (LMEP stack)
 - Rails project
    - nginx
    - mysql
    - rvm
 - Letsencrypt

As of February, 2017, we will be running things under a single Digital Ocean Droplet. If traffic requires it, we may need to move things to multiple machines behind a load balancer.
s

## Development Enviornment Setup

As of February, 2017, the ubuntu/xenial64 box is not useable. see [here](https://bugs.launchpad.net/cloud-images/+bug/1569237). Need to use bento/ubuntu-16.04

 - run `vagrant up` to install a management server and nodes
 - ssh into `mgmt`: `vagrant ssh`
 - symlink to /vagrant which points to `~` in host machine `ln -s /vagrant ~`
 - create `ansible.cfg` and `production` (inventory file) in home directory (see examples)
 - create files that were `.gitignore`d
    - create vars: `mv vars/main.yml.example vars/main.yml`
    - create `.env` file template for rails app
   - load all hosts into known_hosts: `ssh-keyscan web1 >> .ssh/known_hosts`
 - generate an ssh key: `ssh-keygen -t rsa -b 2048`
 - `cd vagrant`
 - download roles from ansible-galaxy `sudo ansible-galaxy install -r roles.yml`
 - give your self the right permission `sudo chown -R vagrant:vagrant /home/vagrant`
 - run a playbook to distribute the generated key: `ansible-playbook playbooks/ssh-addkey.yml --ask-pass`

## in place before running playbook

 - Droplet is created.
 - non root user created with sudo permission
 - can ssh from local machine into Droplet
 - machine can git clone repo via ssh

 - rake tasks
    - `db:seed`
    - `create_admin`
