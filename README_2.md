
IP Address : 18.192.57.79
User grader pass : kgg123*
User grader ssh key:
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,526EF8C30670FA66A3B682E01BE00926

z8B6nSDNxtIXRkRf4fZ0m9UKo+UPZ/XMvTkyan/Lp5rNXdY5WESt9WWGPlVFPw7a
HVtqNCoZTOw++JpVrYTEZTa+SmtJ3ARG5E3dM8rACG2O/b9PwkU/PB4SBwTWqo0g
SzlHDhADxDTZL4wYVmJxCJOIxAunQ+ABzU8a92ORerqt6AmN22bc2NkrQIB4JcE/
valzfks6q4q/ArAmhqiNWiKj6U9emiYwthPxgOPl2omt5iu588nnQhQf0kt2zKLF
TRqzmsZznIPlj9IAov1y72b5x5ylEZCfaRnPK4SbhkQNizjI0k9ZJ4hvj8NNiyj8
qeF2mctMTwnd1SONMCB7POF9Zq57LfzCRYo8GxKQ5gzLB2Rw+xMoE4m45WXKtmuC
pP20ujfxy92+J7AnTPb0UzhcQrkJdzNtX/C34clqCnDdZhhUPgYlik+ufRmQdjyK
MXymZOcI7CMzECZoI0OzJpm72NLXEzpmwvt9tn9k7zWHh0uNc6r+DP6L8zpfmErR
jhLh8w+5wt7L7nPoLnDMKoqtrfb/Lb8OIMuSW8tfNH1n24RihNft2mHmF15BBAIX
Yj5VvJRCX8I4ChPP9BO7bQE8C5ci1OUOb70zyZxACcHSryNrN0FglqcR8GDYj/tl
pZey8U5o50VJwiLOxfX454wjiGftVlpTHNHd2TaWlQ75DxIViAa1NtpyINnGrh/7
j2CShfQBgPaGz9ToVVqQC3o2WbJWBqNA9bzSHq5V82roOGqwsaMKvwH7CNYC95tf
X6glg6UkEIXITYePLjT8XOwrAC7QrSj4J80cWuWx0hg69lAgGt0JB5mBjCrYSex0
ytGtS2gp/ZL7rHI5AVHHRbgiP2DPSEA4o6/2yoXWlrzlFJqYMpJhP0qGlNiJ32F6
E3b16o/o/zkrwGi5AunVPYEl+Z7NXOkCS34rzNEwCubeOsdF2Ab2MBqyxxEw9lt1
npSfNLPMhImA09y8z0cSHm66VGQKPKNGp9Jq+aae7cBeOWvydzqd8+1UC8VdPmtm
BGoBxxnuanDWUrEMilYyd0Y7NQYq7dqXf3pRiN25/7WjAE5Yw2AWR5N8Cl8o+UNu
ddLxb8UWNHRO8fEbupUYUcxU9uAN5DrhNJAMj5rvqi7LiuJjvkJ5uReMv1CO2WR5
GIsoXxlEauneK871fgNSJl5sThtVpsSuTSDOGFt5t5lJVAxWEvbb0tqCLGvhJPe6
rKdMp5tEYnlm+LVfeK5GoXNLjvyQDaabl0vx+2n0dLseGvzuNUGEK+iz1f6zcaIW
+jmYKbFVtCHUsl/rlQUaDFai4wDHtMszWH6jInyl5CxFMbtdE2oZqvVImoEhP6e9
bXfKB0qTpFPVp1tWQ667maPLDwo8EgvNon+8JbP0ziAdwRhkdGFpeqxQZi5zqRrV
LK9liYvYpPnl5fNyKMacWfB6BBkcoDhup5VtqIIOMNYvMked3zxFM5UPnWz/bl7B
cMY1DUjgWUZhko2FGf9l7xSvB75UVDED1vYB5oCE+OW/Upz2uTZIv5oXaKJLKjFr
vCzh8YrCNHTOEZ0BYCHiajabpys9zAuJ952S3fULH9uZmdZroB4G3YVCuDyt8P4f
-----END RSA PRIVATE KEY-----


1. Update packages and set utc
sudo apt-get update
sudo apt-get upgrade
sudo timedatectl set-timezone UTC
sudo update-locale LANG=en_US.utf8 LANGUAGE=en_US.utf8 LC_ALL=en_US.utf8


2. Add grader user and setup
sudo adduser grader
sudo nano /etc/sudoers.d/grader 

Then add the following text grader ALL=(ALL) ALL

sudo su - grader
mkdir .ssh
touch .ssh/authorized_keys 
sudo chmod 700 .ssh
sudo chmod 600 .ssh/authorized_keys 

ssh-keygen
enter file location for pair key 
enter passphrase

  after the generate pair key, open xxxx.pub file and copy key and paste to authorized_keys

sudo nano .ssh/authorized_keys 
  paste and save.


3. Change the SSH port from 22 to 2200 | Enforce key-based authentication | Disable login for root user
sudo nano /etc/ssh/sshd_config
  Then change the following:

Find the Port line and edit it to 2200.
Find the PasswordAuthentication line and edit it to no.
Find the PermitRootLogin line and edit it to no.
Save the file and run sudo service ssh restart


4. Configure the Uncomplicated Firewall (UFW)
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw enable
Later, we will only allow incomming connections for SSH (port 2200)


5. Install apache2 and mod-wsgi for python3 and git
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi-py3
sudo apt-get install git


6. Install and configure PostgreSQL
sudo apt-get install libpq-dev python3-dev
sudo apt-get install postgresql postgresql-contrib
sudo su - postgres
psql
Then

CREATE USER catalog WITH PASSWORD 'password';
CREATE DATABASE catalog WITH OWNER catalog;
\c catalog
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
\q
exit

We should change database engine in our catalog project
engine = create_engine('postgresql://catalog:password@localhost/catalog')


7. Clone the app from GitHub and Configure it
cd /var/www/
sudo mkdir catalog
sudo chown grader:grader catalog
git clone https://github.com/sinanbalaban/Catalog_For_LinuxConf.git catalog
cd catalog
nano catalog.wsgi

Then add the following in catalog.wsgi file

#!/usr/bin/python3
import sys
sys.stdout = sys.stderr

# Add this if you'll create a virtual environment, So you need to activate it
# -------
activate_this = '/var/www/catalog/env/bin/activate_this.py'
with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))
# -------

sys.path.insert(0,"/var/www/catalog")

from app import app as application
application.secret_key = 'super_secret_key'


8. Setup virtual environment and Install pip3 and app requirements
sudo apt-get install python3-pip
sudo -H pip3 install virtualenv
virtualenv env
source env/bin/activate
pip3 install flask packaging oauth2client redis passlib flask-httpauth
pip3 install sqlalchemy flask-sqlalchemy psycopg2 bleach requests


9. Configure apache server
sudo nano /etc/apache2/sites-enabled/000-default.conf
Then add the following content:

# serve catalog app
<VirtualHost *:80>
  ServerName 18.192.57.79 
  ServerAdmin sbalaban54@gmail.com
  DocumentRoot /var/www/catalog
  WSGIDaemonProcess catalog user=grader group=grader
  WSGIScriptAlias / /var/www/catalog/catalog.wsgi

  <Directory /var/www/catalog>
    WSGIProcessGroup catalog
    WSGIApplicationGroup %{GLOBAL}
    Require all granted
  </Directory>

  ErrorLog ${APACHE_LOG_DIR}/error.log
  LogLevel warn
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>


10. Reload & Restart Apache Server
sudo service apache2 reload
sudo service apache2 restart