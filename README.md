# Server information
IP x.x.x.x
URL x.x.x.x.xip.io
SSH port 2200
# Third party software used
Visual Studio Code for writing the code
Putty 0.70 for SSH connection
fireftp to move files to the virtual machine
# Software installed
Apache
Python mod_wsgi
Python PIP
Postgresql
SqlAlchemy
Flask
finger
# User ??? has password ???
# DB user ??? has password ???
# Project DB name is ???

# SSH key file attached on zip file

# Configuration changes
#Lightsail amazon
add ports TCP/2200 and UDP/123
Remove port 22
# with user ubuntu do
# configure ports
sudo ufw status
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw allow ntp
sudo ufw enable
# change SSH port 22 to port 2200
# Look in the file for 'Port 22' and change to 'Port 2200', save file
sudo nano /etc/ssh/sshd_config
sudo ufw deny 22
sudo service ssh restart
# update packages
sudo apt-get update
sudo apt-get upgrade
# disable root login
# Change 'PermitRootLogin without-password' to 'PermitRootLogin no'
sudo nano /etc/ssh/sshd_config
service ssh restart
# set time to UTC
sudo timedatectl set-timezone UTC
# install apache
sudo apt-get install apache2
# install mod_wsgi
sudo apt-get install libapache2-mod-wsgi
# install postgresql
sudo apt-get install postgresql
# verify remote connections to postgresql are disabled
sudo nano /etc/postgresql/9.5/main/pg_hba.conf
# Create a PostgreSQL user called catalog
sudo -u postgres createuser -P catalog
# password for user catalog = catalog
# Create an empty database called catalog
sudo -u postgres createdb -O catalog catalog
# verify user and DB catalog
sudo -u postgres psql
\du
\l
# install python libraries
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy
sudo apt-get install python-pip
sudo pip install --upgrade pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
# install finger
sudo apt-get install finger
# add user grader, password grader
sudo adduser grader
# give sudo access to grader
# add 'grader ALL=(ALL) NOPASSWD:ALL'
sudo nano /etc/sudoers.d/grader
# Set-up SSH keys for user grader
sudo mkdir /home/grader/.ssh
sudo chown grader:grader /home/grader/.ssh
sudo chmod 700 /home/grader/.ssh
# copy ubuntu key
cat .ssh/authorized_keys
# create grader authorized_keys and paste ubuntu key
sudo nano /home/grader/.ssh/authorized_keys
sudo chown grader:grader /home/grader/.ssh/authorized_keys
sudo chmod 644 /home/grader/.ssh/authorized_keys

# with user grader do
sudo mkdir /var/www/catalog
sudo chown grader:grader /var/www/catalog
sudo mkdir /var/www/catalog/catalog
sudo chown grader:grader /var/www/catalog/catalog
# create catalog.wsgi
sudo nano /var/www/catalog/catalog.wsgi
# paste this into catalog.wsgi
    #!/usr/bin/python
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0,"/var/www/catalog/")
    from catalog import app as application
    application.secret_key = 'YOUR_SECRET_KEY'
# copy via all project files and directories into /var/www/catalog/catalog via ftp
# change origins and redirect URL in google credentials
# change file's paths inside project code where needed
# populate database
python /var/www/catalog/catalog/lotsofsongs.py
# Configure Apache2 and mod_wsgi for my app
# create catalog.conf
sudo nano /etc/apache2/sites-available/catalog.conf
# with content
<VirtualHost *:80>
    ServerName x.x.x.x
    ServerAdmin admin@x.x.x.x
    WSGIDaemonProcess catalog user=www-data group=www-data threads=5
    WSGIProcessGroup catalog
    WSGIApplicationGroup %{GLOBAL}
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Require all granted
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Require all granted
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
# Disable the default virtual host
sudo a2dissite 000-default.conf
# Enable the catalog app
sudo a2ensite catalog.conf
# restart apache
sudo service apache2 reload
# Comment on git
I didn't use git for my catalog project, therefor I have no need to use git in this project. Instead I move the files to the server using sftp.
# software to transfer files
Firefox fireftp plugin using sftp connection
# Resource for sftp connection
None, trial an error was usedbut here are the steps
Use Firefox version 56.0b9 or previous (fireftp is not supported in newer versions) or WaterFox browser
Go to https://addons.mozilla.org/en-US/firefox/addon/fireftp/ and add fireftp
Open fireftp inside firefox and create new account
Account name: any (doesn't matter)
Host: x.x.x.x
User: ???
Password: ???
In the tab "connection" select:
Security = sftp
Port = 2200
Private Key = browse and select the same ssh key used for the regular ssh connection
Then OK and connect

# Key-based SSH authentication is enforced
# Change 'PasswordAuthentication yes' to 'PasswordAuthentication no'
sudo nano /etc/ssh/sshd_config
sudo service ssh restart
