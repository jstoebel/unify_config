# unify_config
This is configuration for a VPS using Ansible in order to run the website for Unify Water.

Setup of the development enviornment was taken from [here](https://sysadmincasts.com/episodes/45-learning-ansible-with-vagrant-part-2-4)

## Summary

The playbook will install the following

 - Wordpress (LEMP stack)
 - Rails environment (rvm, passenger)
 - Letsencrypt (TODO)

As of February, 2017, we will be running things under a single Digital Ocean Droplet. If traffic requires it, we may need to move things to multiple machines behind a load balancer.

## set up ansible enviornment
 - create `ansible.cfg` and `production` (inventory file) in home directory (see examples)
 - create vars: `mv vars/main.yml.example vars/main.yml`

## Development Enviornment Setup

We use Vagrant for a development environment. As of February, 2017, the ubuntu/xenial64 box is not useable. see [here](https://bugs.launchpad.net/cloud-images/+bug/1569237). We need to use bento/ubuntu-16.04

 - run `vagrant up` to install a management server and nodes
 - ssh into `mgmt`: `vagrant ssh mgmt`
 - symlink to `/vagrant` to point to `~` in host machine `ln -s /vagrant ~`
 - load all hosts into known_hosts: `ssh-keyscan web1 >> .ssh/known_hosts`
 - generate an ssh key: `ssh-keygen -t rsa -b 2048`
 - `cd vagrant`
 - download roles from ansible-galaxy `sudo ansible-galaxy install -r roles.yml`
 - give yourself the right permission `sudo chown -R vagrant:vagrant /home/vagrant`
 - run a playbook to distribute the generated key: `ansible-playbook playbooks/ssh-addkey.yml --ask-pass`

## SSL in development

I haven't worked out how to handle ssl in development yet.
The nginx config file points to an ssl cert that won't exist on that machine.
The best I can think of is in development to run the playbook and then manually alter the config file.
We'll need a better solution than this.

## before running playbook in production

We assume the following is in place before running the playbook in production

 - Droplet is created.
 - non root user created with password-less sudo permission
 - can ssh from local machine into Droplet
 - machine can git clone repo via ssh
 - python 2!
