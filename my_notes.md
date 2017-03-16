### my notes


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

I'm still stumped but I think the way forward is to figure out how to serve assets from nginx


I think this was the culprit: config.action_controller.asset_host = ENV.fetch("ASSET_HOST", ENV.fetch("APPLICATION_HOST"))

By default, Rails links to these assets on the current host in the public folder, but you can direct Rails to link to assets from a dedicated asset server by setting ActionController::Base.asset_host in the application configuration, typically in config/environments/production.rb. For example, you'd define assets.example.com to be your asset host this way, inside the configure block of your environment-specific configuration files or config/application.rb:

http://api.rubyonrails.org/classes/ActionView/Helpers/AssetUrlHelper.html

absolute links need to be fixed. `/donate` needs to change to `h20-initiative/donate`

## more problems with static assets

see this SO post
http://stackoverflow.com/questions/42521316/rails-5-nginx-passenger-cant-serve-static-assets-404/42522041#42522041

d3 responsivness

 - the secret was in the ratio between width and scale
 - too

## users installing plugins

 - user needed to be in group www-data in order create the proper directories
 - www-data group needs rwx permission on uploads
 - when uploading theme i think it wasn't able to unzip

 - solution: directories in wp-content should have chmod 770. user running needs to belong to group www-data
    - plugins
    - themes
    - upgrade
    - uploads

## ssh-ing into server for capistrano deploy
 - needed to run `ssh-add -L`. not sure why I was able to manually ssh fine though

# 502 bad gateway

only getting it for wp-admin

http://jvdc.me/fix-502-bad-gateway-error-on-nginx-server-after-upgrading-php/
this is where php listens: `listen = /run/php/php7.0-fpm.sock`

possible lead
http://stackoverflow.com/questions/23844761/upstream-sent-too-big-header-while-reading-response-header-from-upstream
