# unify_config
This is configuration for a VPS in order to run the website for Unify Water.
Some of this code comes from https://sysadmincasts.com/episodes/45-learning-ansible-with-vagrant-part-2-4


## Summary

 Everything will be provisioned using ansible.

 - Wordpress (LMAP stack)
 - Rails project
    - apache2
    - mysql
    - rbenv
 - Letsencrypt

As of February, 2017, we will be running things under a single Digital Ocean Droplet. If traffic requires it, we may need to move things to multiple machines behind a load balancer.


## Development Enviornment Setup

 - run `vagrant up` to install a management server and nodes
 - ssh into `mgmt`: `vagrant ssh`
 - symlink to /vagrant which points to `~` in host machine `ln -s /vagrant ~`
 - load all hosts into known_hosts: `ssh-keyscan web1 >> .ssh/known_hosts`
 - generate an ssh key: `ssh-keygen -t rsa -b 2048`
 - create `ansible.cfg` and `production` (inventory file) in home directory (see examples)
 - `cd vagrant`
 - download roles from ansible-galaxy `sudo ansible-galaxy install -r roles.yml`
 - run a playbook to distribute the generated key: `ansible-playbook playbooks/ssh-addkey.yml --ask-pass`
