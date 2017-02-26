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

### update

rbenv is proving trickey to do with ansible. let's try [this](https://galaxy.ansible.com/rvm_io/ruby/)

### difference between provisioning and deployment

not sure which of the following qualify as which.

 - moving .env file (we want a single source of truth of these files)
    - yes this will be needed by capistrano

 - capistrano set up
    - see below
 - run bundle install
    - capistrano will run this for every deploy. forget it.
  - set up admin user for app:
    - this is truly a one time task. even if we add new web servers we don't need to add new admin users

### handling `.env`

 - we want all secrets to be stashed in `.env` file.
 - but this file isn't being loaded in prod per instructions of the gem author. why?
 - Can I use dotenv in production?

 - when I load the envs in `/etc/environment` why can't rails see them:
    - nginx was started by not as a child process of bash. therefore those envs were not available.
    - instead lets use figaro. envs will be loaded at app run time.

dotenv was originally created to load configuration variables into ENV in development. There are typically better ways to manage configuration in production environments - such as /etc/environment managed by Puppet or Chef, heroku config, etc.

I'm going to use figaro instead

 - after adding `application.yml` my browser says Corrupted Content Error

 - why isn't passenger starting:

App 1053 stdout: source=rack-timeout id=0760da6eea2ed26c80b0530cdf406c5b timeout=10000ms state=ready
App 1053 stdout: [72b1cc95-9afb-485a-90d0-381d2de972e6] Started GET "/donate" for 10.0.2.2 at 2017-02-26 13:14:10 +0000
App 1053 stdout: source=rack-timeout id=0760da6eea2ed26c80b0530cdf406c5b timeout=10000ms service=8ms state=completed


The app is timing out!


 diferences tried

 - tried pointing unify at / -> 301
 - tried pointing testapp at /donate -> 404
 - tried pointing testapp at / -> 404


search for: passenger nginx sub uri:
    found: https://www.phusionpassenger.com/library/deploy/nginx/deploy/ruby/#deploying-an-app-to-a-sub-uri-or-subdirectory

ALMOST WORKING!!

I removed these gems:

 - `rack-canonical-host`
 - `rack-timeout`
 - `rails_stdout_logging`

And removed reference to them in `enviornments/production.rb`

But now assets aren't being served

also the app is hanging. it didn't do that a minute ago


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
