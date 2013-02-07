Set up a web server for Rails deployment
========================================

### Enter sudo shell
    sudo -s

### Upgrade system
    apt-get update
    apt-get -y upgrade
    apt-get -y install python-software-properties language-pack-en curl libcurl4-openssl-dev git-core build-essential

### Set language for system
    echo "LC_ALL=en_US.utf8" >> /etc/environment

### Add repositories for Nginx and Node.js
    add-apt-repository -y ppa:nginx/stable
    add-apt-repository -y ppa:chris-lea/node.js
    apt-get update

### Install Nginx
    apt-get -y install nginx

### Install Nodejs (for rails assets pipeline)
    apt-get -y install nodejs npm

### Create 'deploy' user
    sudo adduser deploy

### Add deploy user to sudoers (for now)
    sudo adduser deploy sudo

### Switch to 'deploy' user shell
    su - deploy

### Install rbenv
    curl -L https://raw.github.com/fesplugas/rbenv-installer/master/bin/rbenv-installer | bash

### Add rbenv initializer to .bashrc
    wget -O - https://raw.github.com/PeterHamilton/rails-deploy-setup/master/.bashrc >> ~/.bashrc
    
### Reload bash
    source .bashrc

### Install and set ruby version using rbenv (can take a LONG time)

    rbenv bootstrap-ubuntu-12-04
    rbenv install 1.9.3-p194
    rbenv global 1.9.3-p194

### Setup Gems
    wget -O ~/.gemrc https://raw.github.com/PeterHamilton/rails-deploy-setup/master/.gemrc
    gem install bundler --no-ri --no-rdoc
    rbenv rehash

### Install Rails
    gem install rails

### Install Unicorn
    gem install unicorn
    
### Install Passenger
    gem install passenger

### Enter sudo shell
    sudo -s

### Install Postgres
    sudo add-apt-repository ppa:pitti/postgresql
    sudo apt-get update
    sudo apt-get install postgresql libpq-dev

### Install Passenger & Nginx
    sudo /home/deploy/.rbenv/shims/passenger-install-nginx-module
  - Press Enter
  - Select option 1 (install all defaults)
  - Install to /etc/nginx

### Install script to control nginx
    wget -O init-deb.sh http://library.linode.com/assets/660-init-deb.sh
    sudo mv init-deb.sh /etc/init.d/nginx
    sudo chmod +x /etc/init.d/nginx
    sudo /usr/sbin/update-rc.d -f nginx defaults

### Replace default nginx config with a leaner one
    sudo mv /etc/nginx/conf/nginx.conf /etc/nginx/conf/nginx.conf.old # back up
    sudo wget -O /etc/nginx/conf/nginx.conf https://raw.github.com/PeterHamilton/rails-deploy-setup/master/nginx.conf
  
### Restart nginx to check everything works
    sudo /etc/init.d/nginx stop
    sudo /etc/init.d/nginx start

### Add ubuntu user to www-data group
    usermod -a -G www-data deploy

### Create www folder
    mkdir /var/www

### Change owner of www folder to ubuntu
    chown deploy:www-data /var/www

#TODO (Ignore for Now):

## Extra nginx confs
    sudo mv /etc/nginx/conf.d /etc/nginx/conf
    cd /etc/nginx/conf
    sudo wget https://raw.github.com/h5bp/server-configs/master/nginx/conf/cache-busting.conf
    sudo wget https://raw.github.com/h5bp/server-configs/master/nginx/conf/cross-domain-ajax.conf
    sudo wget https://raw.github.com/h5bp/server-configs/master/nginx/conf/cross-domain-fonts.conf
    sudo wget https://raw.github.com/h5bp/server-configs/master/nginx/conf/expires.conf
    sudo wget https://raw.github.com/h5bp/server-configs/master/nginx/conf/h5bp.conf
    sudo wget https://raw.github.com/h5bp/server-configs/master/nginx/conf/no-transform.conf
    sudo wget https://raw.github.com/h5bp/server-configs/master/nginx/conf/protect-system-files.conf
    sudo wget https://raw.github.com/h5bp/server-configs/master/nginx/conf/x-ua-compatible.conf


