---
title: 'Deploy project with capistrano, nginx and passenger'
layout:   post
category: tutorial-tips
tags:     [rails, rails-6]
feature:  /assets/img/multiple-db-pic1.png
---
Know to deploy with capistrano, nginx and passenger
<!--more-->
##### Setup Capistrano ?
Gem file
```ruby
gem 'capistrano',                 '3.11.2'
gem 'capistrano-rails',           '1.4.0'
gem 'capistrano-passenger',       '0.2.0'
gem 'capistrano-rbenv',           '2.1.4 '
# capistrano-rvm
```
after `bundle install` you can use `cap install` command to auto generate follow file: 
* Capfile
* config/deploy.rb
* staging.rb and production.rb in the config/environments directory

##### Setup Capfile ?

```ruby
require "capistrano/rails"
require "capistrano/passenger"
require "capistrano/rbenv"
#capistrano-rvm
```
##### Setup config/deploy.rb ?

``` ruby
# config valid for current version and patch releases of Capistrano
lock "~> 3.14.1"

set :application, "profile"
set :repo_url, "git@github.com:nvt94/profile.git"
set :branch, 'master'
set :deploy_to, '/var/www/profile'
set :use_sudo, true
set :linked_files, %w(config/database.yml config/application.yml)
set :linked_dirs, fetch(:linked_dirs, []).push('log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'vendor/bundle', 'public/system')

```
##### Setup config/deploy/staging.rb?

```ruby
server "sample-web-server", user: "deploy", roles: %w{app db web}
```

##### config/environments/staging.rb
This option specifies that the master key used to decrypt credentials should be available on our web server.
config.require_master_key = true

###### rails secret
```ruby
EDITOR="mine" bin/rails credentials:edit
The config/master.key and config/credentials.yml.enc files will be generated.
```

##### Setup server ?
install nginx
install passenger

###### Config nginx
* mod-http-passenger.conf file
passenger_root /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini;
passenger_ruby /usr/bin/passenger_free_ruby;

*  site-enabled 
```ruby
server {
 listen 80;
 server_name devorip.com; #replace with your server ip or domain
# Tell Nginx and Passenger where your app’s ‘public’ directory is
 root /var/www/profile/current/public;
# Turn on Passenger
 passenger_enabled on;
 passenger_ruby /home/deploy/.rbenv/versions/2.6.6/bin/ruby;
}
```

###### Setup ssh and git ?
###### Setup shared folder ?


##### Deploy ???
cap production deploy
cap staging deploy

