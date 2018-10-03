# Linux Server Configuration

This project consists of a description of key information to access the projects.
Below you can also find the software installed and operations executed to configure the server.
The application is hosted in an Ubuntu Server on Amazon Lightsail.

## IP and SSH port

IP address of the server: `34.243.193.157`

SSH port: `2200`

## URL

URL of the web application: `http://34.243.193.157.xip.io`

## Software installed

* Apache HTTP Server: `sudo apt-get install apache2`
* Python WSGI adapter module for Apache: `sudo apt-get install libapache2-mod-wsgi`

* User information lookup program: `sudo apt-get install finger`

* PostgreSQL, object-relational SQL database (supported version): `sudo apt-get install postgresql`

* Python package installer: `sudo apt-get install python-pip`
* Flask a microframework for Python: `sudo pip install Flask`
* Object Relational Mapper for SQL: `sudo pip install sqlalchemy`
* Simple integration of Flask and WTForms: `sudo pip install flask_wtf`
* Client library for accessing resources protected by OAuth 2.0: `sudo pip install oauth2client`
* HTTP library for Python: `sudo pip install requests`

## Third-party resources used to complete the project

* [Change the default SSH port](http://linuxlookup.com/howto/change_default_ssh_port)
* [Flask Hello World App with Apache WSGI](https://www.bogotobogo.com/python/Flask/Python_Flask_HelloWorld_App_with_Apache_WSGI_Ubuntu14.php)
* [Timezone setting in Linux](https://unix.stackexchange.com/questions/110522/timezone-setting-in-linux)
* [secret key not set in flask session](https://stackoverflow.com/questions/26080872/secret-key-not-set-in-flask-session#26080974)
* [Sqlite3, OperationalError: unable to open database file](https://stackoverflow.com/questions/4636970/sqlite3-operationalerror-unable-to-open-database-file)
* [Why do I get sqlite error, “unable to open database file”?](https://serverfault.com/questions/57596/why-do-i-get-sqlite-error-unable-to-open-database-file)