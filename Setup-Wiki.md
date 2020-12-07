[![Build Status](https://travis-ci.org/openaustralia/morph.png?branch=master)](https://travis-ci.org/openaustralia/morph) [![Code Climate](https://codeclimate.com/github/openaustralia/morph.png)](https://codeclimate.com/github/openaustralia/morph) 

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [morph.io: A scraping platform](#morphio-a-scraping-platform)
    - [Dependencies](#dependencies)
    - [Installing Docker](#installing-docker)
        - [On Linux](#on-linux)
        - [On Mac OS X](#on-mac-os-x)
    - [Starting up Elasticsearch](#starting-up-elasticsearch)
    - [To Install Morph](#to-install-morph)
<!-- markdown-toc end -->
## Dependencies
Ruby 2.3.1, Docker, MySQL, SQLite 3, Redis, mitmproxy.
(See below for more details about installing Docker)

Development is supported on Linux (Ubuntu 16.04 works best; Ubuntu 18.04 is possible with some setup) and Mac OS X.
Docker images:
* [openaustralia/buildstep](https://github.com/openaustralia/buildstep) - Base image for running scrapers in containers

## Installing Docker

### On Linux

Just follow the instructions on the [Docker site](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/).

Your user account should be able to manipulate Docker (just add your user to the `docker` group).

### On Mac OS X

Install [Docker for Mac](https://docs.docker.com/docker-for-mac/install/).

## Starting up Elasticsearch

Morph needs Elasticsearch to run. We've made things easier for development by using docker
to run Elasticsearch.

    docker-compose up

## To Install Morph
To run Morph on you mechine first take colne of git repository by following the command

```
git clone git@github.com:techcreatix/morph.git
```
Then:
```
cp config/database.yml.example config/database.yml
cp env-example .env
```
### Bundle Install 

**Befor installing bundle replace the following gems:**

Old | New
------------ | -------------
gem "sync" | gem 'render_sync', '~> 0.5.0'
gem "puma" | gem 'puma', '~> 5.0', '>= 5.0.2'
gem "omniauth-github" | gem 'omniauth-github', '~> 1.4' 
>OR **Latest version** of 'omniauth-github'.


As now we are using **render_sync** so we've to make some changes in some files.
Old | New
---------------- | ----------------
Sync | RenderSync
require "sync"| require "render_sync"

##### Files:

```
scraper.rb
cache_digests.rb
runner.rb
sync.ru
create_scraper_progress.rb
```
Also you have **mysql lib**
```
sudo apt-get install libmysqlclient-dev
```

You should have mysql server.To Check its installed or not:
```
qasim-hp-probook-650-g2:~$ sudo mysql --version
mysql  Ver 8.0.22-0ubuntu0.20.04.3 for Linux on x86_64 ((Ubuntu))
```
If mysql server is not install then: 
```
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
```
For further details of [mysql-server](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04).

Edit `config/database.yml` with your database settings

```
development:
  adapter: mysql2
  database: scraping_development
  username: root
  password: password
  pool: 5
  timeout: 5000

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  adapter: mysql2
  database: scraping_test
  username: root
  password: password

production:
  adapter: mysql2
  database: scraping_production
  username: root
  password: password
  # This needs to be high to handle the concurrency in sidekiq
  pool: 25

  > **Here** 
    - we are using mysql so you should have install mysql serer on you system.
    - username and password are basically credentials of user which you created in mysql.  
    
```


Create an [application on GitHub](https://github.com/settings/applications/new) so that morph.io can talk to GitHub. Fill in the following values

* Application name: __Morph (dev)__
* Homepage URL: http://127.0.0.1:3000
* Authorization callback URL: http://127.0.0.1:3000/users/auth/github/callback
* Application description: You can leave this blank

Note the use of 127.0.0.1 rather than localhost. Use this or it won't work.

In the `.env` file, fill in the *Client ID* and *Client Secret* details provided by GitHub for the application you've just created.

Now setup the databases:

    bundle exec dotenv rake db:setup

Now you can start the server

    bundle exec dotenv foreman start

and point your browser at [http://127.0.0.1:3000](http://127.0.0.1:3000)

To get started, log in with GitHub. There is a simple admin interface
accessible at [http://127.0.0.1:3000/admin](http://127.0.0.1:3000/admin). To
access this, run the following to give your account admin rights:

    bundle exec rake app:promote_to_admin
