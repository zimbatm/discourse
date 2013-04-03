# Discourse "Quick-and-Dirty" Install Guide

We have deliberately left this section lacking. From our FAQ:

> Discourse is brand new. Discourse is early beta software, and likely to remain so for many months.
> Please experiment with it, play with it, give us feedback, submit pull requests – but any consideration
> of fully adopting Discourse is for people and organizations who are eager to live on the bleeding and broken edge.

When Discourse is ready for primetime we're going to provide several robust and easy ways to install it.
Until then, if you are feeling adventurous you can try to set up following components.

- Postgres 9.1
 - Enable support for HSTORE
 - Create a discourse database and seed it with a basic image
- Redis 2.6
- Ruby 1.9.3
 - Install all rubygems via bundler
 - Edit database.yml and redis.yml and point them at your databases.
 - Run `rake db:seed_fu` to add seed data
 - Prepackage all assets using rake
 - Run the Rails database migrations
 - Run a sidekiq process for background jobs
 - Run a clockwork process for enqueing scheduled jobs
 - Run several Rails processes, preferably behind a proxy like Nginx.




# Ubuntu Precise64 on a single server as of 3 Apr 2013

It goes something like this:

```
sudo apt-add-repository ppa:brightbox/ruby-ng
sudo apt-get update
sudo apt-get install git ruby1.9.3 libpq-dev nginx postgresql redis-server build-essential libxml2-dev libxslt-dev tmux
sudo su - postgres
psql
create database discourse;
create role discourse login createdb;
^D^D
git clone https://github.com/discourse/discourse.git
cd discourse
cp config/database.yml.sample config/database.yml
cat config/database.yml.sample | sed "s/discourse_development/discourse/g" > config/database.yml
bundle install --deployment --without development test
export RAILS_ENV=production
export SECRET_TOKEN=`bundle exec rake secret`
RAILS_ENV=production bundle exec rake db:migrate db:seed_fu
# Fail !
# PG::Error: ERROR:  could not open extension control file "/usr/share/postgresql/9.1/extension/hstore.control": No such file or directory
```
