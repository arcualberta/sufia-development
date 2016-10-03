#  Setup Sufia 7 in a production environment


## Install RVM

```bash
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\\curl -sSL https://get.rvm.io | bash -s stable
rvm install 2.3.1
rvm gemset create production
rvm use 2.3.1@production --default
gem install bundler
gem install rails -v 4.2.7.1
```

##  Install java 8

```bash
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer

```

##  Install Jetty 9

Download the latest stable version of jetty

```bash
lynx http://download.eclipse.org/jetty/stable-9/dist/
```

Continue with installation procedure

```bash
tar zxvf jetty-distribution-9.3.8.v20160314.tar.gz
sudo mv jetty-distribution-9.3.8.v20160314 /opt/jetty
cp -a /opt/jetty/bin/jetty.sh /etc/init.d/jetty
sudo useradd jetty -U -s /bin/false
sudo chown -R jetty:jetty /opt/jetty
cp /opt/jetty/bin/jetty.sh /etc/init.d/jetty
sudo vi /etc/default/jetty
```

Add the following lines

```
JAVA_HOME=/usr/bin/java
JETTY_HOME=/opt/jetty
NO_START=0
JETTY_ARGS=jetty.port=8085
JETTY_HOST=0.0.0.0
JETTY_USER=jetty
```

##  Install solr

```bash
wget http://apache.mirror.gtcomm.net/lucene/solr/6.1.0/solr-6.1.0.tgz
tar zxvf solr-6.1.0.tgz solr-6.1.0/bin/install_solr_service.sh --strip-components=2

```

##  Install passenger

```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
sudo apt-get install -y apt-transport-https ca-certificates
```

## # Add our APT repository
``` bash
sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger precise main > /etc/apt/sources.list.d/passenger.list'
sudo apt-get update
sudo apt-get install -y libapache2-mod-passenger
sudo a2enmod passenger
sudo apache2ctl restart
```

##  Install and start Redis 

```bash
sudo apt-get install redis-server 
```

##  set secret

```bash
bundle exec rake secret
```

Copy that key to the secrets file

```bash
vim config/secrets.yml 
chmod 700 config db
chmod 600 config/database.yml config/secrets.yml
```

##  Create Sufia 7 based app

```bash
rails new projectName --skip-bundle -d mysql --without development test
```

Add required gems to Gemfile

```
gem 'sufia', '7.1.0'
gem 'sqlite3'
```
##  Bundle install

```bash
bundle install
```

Define database connection on `config/database.yml`

```
development:
  adapter: sqlite3
  database: db/development.sqlite3
  pool: 5
  timeout: 5000

production:
  adapter: mysql2
  encoding: utf8
  database: sufia7_production
  username: sufia7
  password: PASSWORD
  host: arrl-db001.artsrn.ualberta.ca

```

##  Configure sufia app

Create install files

```bash
rails generate sufia:install -f
```

Create work

```bash
rails generate sufia:work Work
```

Run migrations

```bash
RAILS_ENV=production rake db:migrate
```

Set secret key

```bash
rake secret
```

Add secret to `config/secrets.yml`

Populate project

```bash
rails generate sufia:install -f
rails generate sufia:work Work
RAILS_ENV=production rake db:migrate
```

Edit file ` config/role_map.yml`

```bash
production:
  archivist:
  admin_policy_object_editor:
    - orodrigu@ualberta.ca  
  donor:
  researcher:
  patron:
    - orodrigu@ualberta.ca

```

Add secret key to `config/initializers/devise.rb` by removing comment on line `config.secret_key`

## # Prepare sufia for Solr

Add solr core configuration on `config/solr.yml`

```
production:
  url: http://127.0.0.1:8983/solr/blacklight-core
```

## # Prepare fedora

Add fedora core configuration on `config/fedora.yml`

```
production:
  user: fedoraAdmin
  password: fedoraAdmin
  url: http://127.0.0.1:8085/fedora/rest
  base_path: /prod

```

Precompile assets

```bash
RAILS_ENV=production rake assets:precompile
```

##  Configure passenger

Get the configuration line

```bash
passenger-config about ruby-command
```

Will output something similar to the following line

```
PassengerRuby /home/sufia7/.rvm/gems/ruby-2.3.1@production/wrappers/ruby
```

Edit the site configuration file

```bash
vim /etc/apache2/sites-available/YOUR-FQDM
```

and add the following

```html
<VirtualHost *:80>
  ServerName sufia.artsrn.ualberta.ca

  # Tell Apache and Passenger where your app's 'public' directory is
  DocumentRoot /var/sites/sufia/YOUR-FQDM/public

  PassengerRuby /var/sites/sufia/home/sufia7/.rvm/gems/ruby-2.3.1@production/wrappers/ruby

  # Relax Apache security settings
  <Directory /var/sites/sufia/YOUR-FQDM/public>
    Allow from all
    Options -MultiViews
    # Uncomment this if you're on Apache >= 2.4:
    #Require all granted
  </Directory>
</VirtualHost>
``` 

Enable the site

##  Fits

add fits.sh location path to `config/initializers/sufia.rb`

Start all services including apache and the production environment will be up and running.