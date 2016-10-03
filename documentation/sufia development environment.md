# Sufia development environment setup


## RVM Setup

Install RVM

```bash
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable 
```

Install Ruby 2.3.1, create a gemset for sufia and set them as default.

```bash
rvm install 2.3.1
rvm gemset create sufia
rvm use 2.3.1@sufia --default
gem install bundler
gem install rails -v 4.2.7.1
```

## Install java 8

```bash
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer

```

## Installing sufia

```bash
rails new sufiaDev
cd sufiaDev
```

Add the following gem

```
gem 'sufia', '7.1.0'
```

Install all gems and their dependencies	

```bash
bundle install
```

```bash
rails generate sufia:install -f
rails generate sufia:work Work
rake db:migrate
```

If you get the error

```
Gem Load Error is: Could not find a JavaScript runtime. See https://github.com/rails/execjs for a list of available runtimes.
```
When running `rails generate sufia:install -f` you will need to install a JavaScript runtime like nodejs.

# Install fits.sh

```
wget http://projects.iq.harvard.edu/files/fits/files/fits-0.8.5.zip
sudo unzip fits-0.8.5.zip -d /opt/
```

add fits.sh path `/opt/fits-0.8.5` to `config/initializers/sufia.rb`


## Start Redis, Solr, Fedora, and Sufia 

If you have not installed `redis-server` please do so now.

Finally start the rest of your services.

```bash
redis-server
rake hydra:server
```

Happy coding!



