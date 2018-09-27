Configuring a Linux server

About
This project is a baseline installation of a Linux server and prepare it to host my web applications. secured my server from a number of attack vectors, installed and configure a database server, and deployed one of my existing web applications onto it.

Server details
IP address: 52.56.121.248

SSH port: 2200

Configuration changes
Add user
Add user grader with command: sudo adduser grader

Add user grader to sudo group
Assuming your Linux distro has a sudo group (like Ubuntu 16.04), simply add the user to this admin group:

sudo usermod -a -G sudo grader
Update all currently installed packages
Update the package indexes apt-get update

To actually upgrade the installed packages apt-get upgrade

Set-up SSH keys for user grader
As root user do:

mkdir /home/grader/.ssh
chown grader:grader /home/grader/.ssh
chmod 700 /home/grader/.ssh
cp /root/.ssh/authorized_keys /home/grader/.ssh/
chown grader:grader /home/grader/.ssh/authorized_keys
chmod 644 /home/grader/.ssh/authorized_keys
Can now login as the grader user using the command: ssh grader@35.178.34.134 -p 22 -i ~/.ssh/linuxrsa

Disable root login
Change the PasswordAuthentication line to no instead of yes in the file sudo nano /etc/ssh/sshd_config:

So it reads:
PasswordAuthentication no
Do sudo service ssh restart for the changes to take effect.

Change timezone to UTC
Check the timezone with the date command. This will display the current timezone after the time. If it's not UTC change it like this:

sudo timedatectl set-timezone UTC

Add SSH port 2200
Reconfigure the port for the ssh server: sudo nano /etc/ssh/sshd_config

and add: port 2200

Then reload the configuration: sudo service ssh force-reload

and connect via:

ssh grader@35.178.34.134 -p 2200 -i ~/.ssh/linuxrsa

Configuration Uncomplicated Firewall (UFW)
By default, block all incoming connections on all ports:

sudo ufw default deny incoming

Allow outgoing connection on all ports:
sudo ufw default allow outgoing

Allow incoming connection for SSH on port 2200:
sudo ufw allow 2200/tcp

Allow incoming connections for HTTP on port 80:
sudo ufw allow www

Allow incoming connection for NTP on port 123:
sudo ufw allow ntp

To check the rules that have been added before enabling the firewall use:
sudo ufw show added

To enable the firewall, use:
sudo ufw enable

To check the status of the firewall, use:
sudo ufw status

Install Apache to serve a Python mod_wsgi application
Install Apache:
sudo apt-get install apache2

Install the libapache2-mod-wsgi package:
sudo apt-get install libapache2-mod-wsgi

Install and configure the PostgreSQL database system
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
Create a PostgreSQL role named catalog and a database named catalog
sudo -u postgres -i
postgres:~$ createuser catalog
postgres:~$ createdb catalog
postgres:~$ psql catalog
postgres=# ALTER DATABASE catalog OWNER TO catalog;
postgres=# ALTER USER catalog WITH PASSWORD 'catalog';
postgres=# \q
postgres:~$ exit

Install Flask, SQLAlchemy, etc
Issue the following commands:
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf
Install Git version control software
sudo apt-get install git

Clone the repository that contains Project 4 Catalog app
cd /var/www
sudo mkdir project4
sudo chown -R grader:grader project4
cd project4
sudo git clone https://github.com/basmaashouur/project4.git project4
Configure the Catalog app
Change app.py to __init__.py
Provide the full path (or location) to all the files, instead of just the filename
Configure Google OAuth
Add the public IP address of your server to the “Authorized javascript origins”
Add the public IP address to the client secrets JSON file
Configure the web application to connect to the PostgreSQL database instead of a SQLite database
sudo nano /var/www/project4/project4/app.py
