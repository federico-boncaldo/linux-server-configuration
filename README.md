# Linux Server Configuration

This project consists of a description of key information to access the projects.
Below you can also find the software installed and operations executed to configure the server.
The application is hosted in an Ubuntu Server on Amazon Lightsail.

## IP and SSH port

IP address of the server: `34.243.193.157`

SSH port: `2200`

## URL

URL of the web application: `http://34.243.193.157.xip.io`


## Configuration made

### Update all currently installed packages

1. `sudo apt-get update`
2. `sudo apt-get upgrade`
3. `sudo apt-get update && sudo apt-get dist-upgrade`

### SSH configuration

Edit the SSH configuration file

1. `sudo vi /etc/ssh/sshd_config`

Add the following lines

2. `Port 2200`

3. `PermitRootLogin no`

4. `DenyUsers root`

Restart the SSH server

5. `sudo /etc/init.d/ssh restart`

### Uncomplicated firewall (UFW) configuration

1. `ufw allow 2200`
2. `ufw allow www`
3. `ufw allow 123`
4. `ufw enable`

### Grader user configuration

Create the new user

1. `sudo adduser grader`

Create a file to grant sudo access to the user

2. `sudo touch /etc/sudoers.d/grader`

Add this content to the file

3.
`sudo vi/etc/sudoers.d/grader`
```
# User rules for grader
	grader ALL=(ALL) NOPASSWD:ALL
```

Create a private and a public key with the following command

4. `ssh-keygen`

5. copy the content of the public key in `/home/grader/.ssh/authorized_keys`
6. the private will be used to access the server with this command `ssh grader@34.243.193.157 -p 2200 -i name_of_the_private_key.rsa`

### Apache installation and configuration

Install Apache and wsgi module and activate it

1. `sudo apt-get install apache2`
2. `sudo apt-get install sudo apt-get install libapache2-mod-wsgi`
3. `sudo a2enmod wsgi`

Configure Apache

4. `sudo vi /etc/apache2/sites-enabled/000-default.conf`
5.
```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/item-catalog/

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog /var/www/html/item-catalog/logs/error.log
        CustomLog /var/www/html/item-catalog/logs/access.log combined

         WSGIDaemonProcess myapp user=www-data group=www-data threads=5
         WSGIScriptAlias / /var/www/html/item-catalog/myapp.wsgi

        <Directory /var/www/html/item-catalog>
                WSGIProcessGroup myapp
                WSGIApplicationGroup %{GLOBAL}
                Order deny,allow
                Allow from all
        </Directory>

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>
```


### PostgreSQL installation and configuration

Install PostgreSQl

1. `sudo apt-get install postgresql`

Create `catalog` user and `catalog` database

2. `sudo -u postgres psql postgres`
3. `CREATE ROLE catalog LOGIN PASSWORD 'user_password';`
4. `CREATE DATABASE catalog WITH OWNER = catalog`;

### Clone and setup the Item Catalog project

1. `cd /var/www/html`
2. `sudo git clone https://github.com/federico-boncaldo/linux-server-configuration.git`
3. `cd item-catalog`
4. `sudo touch myapp.wsgi`
5. `sudo vi myapp.wsgi`
6.
```
#!/usr/bin/env python2.7

import sys
sys.path.insert(0,"/var/www/html/item-catalog")

from application import app as application
```

Install the Python package installer and all the modules necessary for the web application.

7. `sudo apt-get install python-pip`
8. `sudo pip install Flask`
9. `sudo pip install sqlalchemy`
10. `sudo pip install flask_wtf`
11. `sudo pip install oauth2client`
12. `sudo pip install requests`
13. `sudo pip install psycopg2`

Create the logs folder inside the project

13. `sudo mkdir logs`

Give ownership of the entire project to the user `www-data`

14. `sudo chown -R www-data:www-data`

Make few changes to `application.py`

15. `sudo vi application.py`

Use the absolute path to locate the `client_secrets.json` file

16. `open('/var/www/html/item-catalog/client_secrets.json', 'r').read())['web']['client_id']`
17. `oauth_flow = flow_from_clientsecrets('/var/www/html/item-catalog/client_secrets.json', scope='')`

Import the os module and move the secret key outside the if statement

18.
```
import os
```

```
app.secret_key = os.urandom(24)
if __name__ == '__main__':
```

Change database URL in `application.py` and `database_setup.py`

19. `'postgresql://catalog:catalogpassword@localhost/catalog')`


Give permission to access, modify and read `.git` only to `root` user

20. `sudo chown root -R .git`
21. `sudo chmod 700 -R .git`

Reload Apache web server

22. `sudo /etc/init.d/apache2 reload`

###Set up Google credentials

1. Access `https://console.developers.google.com/` and select the project in the top left menu.
2. Click on `Credentials` in the left menu.
3. Click on `Oauth consent screen` and under authorised domains add `xip.io` and click on `Save`
4. Click on `Credentials` and locate your Id and click on `Edit Oauth client`, it's the pen icon.
5. Under `Authorised JavaScript origins` and `Authorised redirect URIs` add the URL of the web application `http://34.243.193.157.xip.io` and click on `Save`

## Software installed

* Apache HTTP Server: `sudo apt-get install apache2`
* Python WSGI adapter module for Apache: `sudo apt-get install libapache2-mod-wsgi`

* User information lookup program: `sudo apt-get install finger`

* PostgreSQL, object-relational SQL database: `sudo apt-get install postgresql`

* Python package installer: `sudo apt-get install python-pip`
* Flask a microframework for Python: `sudo pip install Flask`
* Object Relational Mapper for SQL: `sudo pip install sqlalchemy`
* Simple integration of Flask and WTForms: `sudo pip install flask_wtf`
* Client library for accessing resources protected by OAuth 2.0: `sudo pip install oauth2client`
* HTTP library for Python: `sudo pip install requests`
* Psycopg is the most popular PostgreSQL adapter for the Python programming language: `sudo pip install psycopg2`



## Third-party resources used to complete the project

* [Change the default SSH port](http://linuxlookup.com/howto/change_default_ssh_port)
* [Flask Hello World App with Apache WSGI](https://www.bogotobogo.com/python/Flask/Python_Flask_HelloWorld_App_with_Apache_WSGI_Ubuntu14.php)
* [Timezone setting in Linux](https://unix.stackexchange.com/questions/110522/timezone-setting-in-linux)
* [secret key not set in flask session](https://stackoverflow.com/questions/26080872/secret-key-not-set-in-flask-session#26080974)
* [Sqlite3, OperationalError: unable to open database file](https://stackoverflow.com/questions/4636970/sqlite3-operationalerror-unable-to-open-database-file)
* [Why do I get sqlite error, “unable to open database file”?](https://serverfault.com/questions/57596/why-do-i-get-sqlite-error-unable-to-open-database-file)
* [How To Tune your SSH Daemon Configuration on a Linux VPS](https://www.digitalocean.com/community/tutorials/how-to-tune-your-ssh-daemon-configuration-on-a-linux-vps)
* [Ubuntu Server message says packages can be updated, but apt-get does not update any](https://serverfault.com/questions/265410/ubuntu-server-message-says-packages-can-be-updated-but-apt-get-does-not-update)
* [SQLAlchemy - Engine Configuration](https://docs.sqlalchemy.org/en/latest/core/engines.html)
* [What is the www-data user?](https://askubuntu.com/questions/873839/what-is-the-www-data-user)
* [Mastering Markdown](https://guides.github.com/features/mastering-markdown/)
* [How do I login and authenticate to Postgresql after a fresh install?](https://stackoverflow.com/questions/2172569/how-do-i-login-and-authenticate-to-postgresql-after-a-fresh-install)