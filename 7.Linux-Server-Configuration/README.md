﻿# Linux Server Configuration

Baseline linux server using Amazon Lightsail prepared to host web applications.
The server will be secured using a firewall with a configured database.
This server is than used to host a previous Udacity web application.

## Server Specifics

IP: ~~http://54.208.105.233/~~

Instance has been shut down.

## Package Install from Ubuntu (See packages.ubuntu.com for descriptions)

```
sudo apt-get install finger
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi
sudo apt-get install postgresql

sudo apt-get install git
```

**Note**: After modifying the WSGIScriptAlias, restart Apache <br/>
`sudo apache2ctl restart`

## User Management

Added user grader <br/>
`sudo adduser grader` <br/>

Giving grader sudo permission <br/>
`sudo usermod -aG sudo grader`<br/>

Confirm user with Finger <br/>
`finger grader`<br/>

## Installing Public Key

```
mkdir .ssh
touch .ssh/authorized_keys
nano .ssh/authorized_keys
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
```

## Install Flask, SQLAlchemy, and Oauth2

```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
```

## Create postgresql user

`sudo -u postgres createuser -P grader`

## Create empty DB

`sudo -u postgres createdb -O grader catalogDB`

**Note**: You can view all the databases using the command below:

`psql -U 'USER' -l`

## Clone repository from Catalog project

```
cd /srv
sudo mkdir Catalog
sudo chown www-data:www-data Catalog
sudo -u www-data git clone https://github.com/GitMikeZ/Udacity.git Catalog
```

**Note**: Cloned repository's default branch must be set to the Catalog branch

## Modify catalog.wsgi file to the following

Create new .wsgi file in Catalog directory and follow code 

```python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, '/srv/Catalog/')

from Catalog import app as application

application.secret_key = 'SECRET KEY'
```

**Note**: Be careful of directory pathing

## Create and modify catalogapp.conf in directory /etc/apache2/sites-enabled

```
<VirtualHost *:80>
            ServerName 54.208.105.233
            ServerAdmin grader@54.208.105.233
            WSGIScriptAlias / /srv/Catalog/Catalog/catalog.wsgi
            <Directory /srv/Catalog/Catalog>
                    Require all granted
            </Directory>
            Alias /static /srv/Catalog/Catalog/static
            <Directory /srv/Catalog/Catalog/static/>
                    Require all granted
            </Directory>
            ErrorLog ${APACHE_LOG_DIR}/error.log
            LogLevel warn
            CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

**Note:** To find a file, use command: ```sudo find / -name catalogapp.conf``` 

Restart apache server: `sudo service apache2 restart`

### Use a2dissite command to disable the 000-default configuration file

`sudo a2dissite 000-default` <br/>

`sudo a2ensite catalogapp`

**Note:** To view error logs, use command ```sudo less /var/log/apache2/error.log```

## Update and Upgrades

`sudo apt-get update` <br/>
`sudo apt-get upgrade`

## Remove Unrequired packages

`sudo apt-get autoremove`

## Disable root login 

### Altering file /etc/ssh/sshd_config
`PermitRootLogin no` <br/>
`PasswordAuthentication no`

### Restart ssh
`service ssh restart`

## Configure Firewall

### Check of firewall status
`sudo ufw status` 

### Block everything coming in
`sudo ufw default deny incoming`

### Allow everything going out
`sudo ufw default allow outgoing`

### Allow ssh
`sudo ufw allow ssh`

### Open ports for Firewall
```
sudo ufw allow www
sudo ufw allow 2200/tcp
sudo ufw allow ntp
sudo ufw enable
```

**Note:** Show added ports ```sudo ufw show added```











