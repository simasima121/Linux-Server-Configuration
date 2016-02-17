# Project: Linux Server Configuration
A baseline installation of Ubuntu Linux on a virtual machine to host a Flask web application I developed earlier in the course [Catalog app](https://github.com/simasima121/catalog).
This includes installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

## Step by Step Walkthrough

### 1 - Launching the VM and SSH into the server
Source: [Udacity](https://www.udacity.com/account#!/development_environment)

1. Create new folder 
2. Downloaded Private Key and record Public IP address
3. Moved the private key file into ~/.ssh folder:
	`mv ~/Downloads/udacity_key.rsa ~/.ssh/` 
4. Set file rights (owner can read and write):
	`chmod 600 ~/.ssh/udacity_key.rsa`
5. SSH into Virtual Machine:
	`ssh -i ~/.ssh/udacity_key.rsa root@<Public IP address>`

### 1.a - Install finger program
The finger program provides status reports on a particular computer system or user.

Install with the following command:

`apt-get install finger` 

### 2 - Create new user named grader and grant user sudo permissions
Source: [How To Add and Delete Users on an Ubuntu 14.04 VPS](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps)

1. Create a new user called grader:
	`adduser grader`
2. Ensure user has been created successfully:
	`finger grader`
3. To grant user sudo privileges:
  1. Type:
		`visudo`
  1. Search for line:
		`root ALL=(ALL:ALL) ALL`
  1. Copy format of that line and change word **root** to grader i.e. new user:
		`<new user> All=(ALL:ALL) ALL`

### 2.a - Set up SSH access for new user

1. On **local machine**, create key pair:
	`ssh-keygen`
2. On **remote server**, switch into new user home directory:
	`su - <new user>`
2. Create a .ssh folder in the new user home directory:
	`mkdir .ssh`
3. Create an authorized_keys file where you place your public key:
	`touch .ssh/authorized_keys`
4. On **local machine**, read out the contents of public key:
	`cat ~/.ssh/<filename>.pub`
5. Copy the contents of the public key
6. On the **remote server**, paste the contents on the public key into the **authorized_keys** file:
	`nano .ssh/authorized_keys` and paste
7. Set file permissions on the .ssh directory:
	`chmod 700 .ssh`
	`chmod 644 .ssh/authorized_keys`
8. Install ssh server login:
	`sudo apt-get install openssh-server`

### 2.b - Login as new user:

1. In **terminal** login in as new user:
	`ssh <new user>@<Public IP address> -p <port number> -i ~/.ssh/<private key filename>`

### 3 - Update all currently installed packages

1.	Updating the installed packages:
	`sudo apt-get update`
2. Installing updated packages:
	`sudo apt-get install`
3. Remove packages that are no longer needed:
	`sudo apt-get autoremove`

### 3.a - Stop sudo error:
Source: [Stack Overflow](http://stackoverflow.com/questions/33441873/aws-error-sudo-unable-to-resolve-host-ip-10-0-xx-xx)

1. As root user, navigate to /etc folder
2. Modify hosts file:
	`nano hosts`
3. Under Localhost, declare private ip:
	`10.0.xx.xx ip-10-0-xx-xx` 

### 4 - Configure local timezone to UTC:

1. Execute the following command:
	`sudo dpkg-reconfigure tzdata`
2. Find UTC timezone and press enter

### 5 - Change SSH port from 22 to 2200
Source: [SSH Essentials: Working with SSH Servers, Clients, and Keys](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys)

1. Open the sshd_config file on the server with root privileges:
	`sudo nano /etc/ssh/sshd_config`
2. Search for line:
	`port 22`
3. Change port that SSH runs on to 2200:

	```
	#port 22
	port 2200
	```
4. Save, close then restart:
	`sudo service ssh restart`

### 5.a - Disabling root login

1. Open SSH daemon config file on server:
	`sudo nano /etc/ssh/sshd_config`
2. Search for directive called:
	`PermitRootLogin`
3. Change value to no:
	`PermitRootLogin no`
4. Save, close then restart:
	`sudo service ssh restart`

### 6 - Configure the Uncomplicated Firewall(UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

1. Check status of firewall is inactive:
	`sudo ufw status`
2. Initially block all incoming connections:
	`sudo ufw default deny incoming`
3. Allow all outgoing connections:
	`sudo ufw default allow outgoing`
4. Allow incoming connection for SSH (port 2200):
  1. Allow incoming tcp connections on port 2200:
  		`sudo ufw allow 2200/tcp`
5. Allow HTTP port (port 80):
	`sudo ufw allow 80/tcp`
6. Allow NTP port (port 123):
	`sudo ufw allow 123/udp`
7. Enable the firewall:
	`sudo ufw enable`
8. Confirm firewall set up as indicated:
	`sudo ufw status`

### 7 - Install and configure Apache to serve a Python mod-wsgi app
Source: [Udacity](https://www.udacity.com/course/viewer#!/c-ud299-nd/l-4340119836/m-4818948614)

1. Install apache:
	`sudo apt-get install apache2`
2. Check apache is installed correctly by going to browser and typing in servers Public IP address - It should say 'It works' on the top of the page.
3. Install mod_wsgi:
	`sudo apt-get install libapache2-mod-wsgi python-dev`
4. Enable the mod_wsgi module:
	`sudo a2enmod wsgi`

### 7.a - Deploying a Flask application on Ubuntu VPS
Source: [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

1. Create a flask app
  1. Change directory to /var/www directory:
		`cd /var/www`
  2. Create application directory structure:
		`sudo mkdir catalog`
  3. Change directory to catalog directory:
		`cd catalog`
  4. Create another directory catalog:
		`sudo mkdir catalog`
  5. Move inside this directory and create 2 subdirectories *static* and *templates*:
		```
		cd catalog
		sudo mkdir static templates
		```
  6. Create file that contains flask application logic:
		`sudo nano __init__.py`
  7. Add following logic to the file - paste in the code:
		
		```
		from flask import Flask
		app = Flask(__name__)
		@app.route("/")
		def hello():
	    	return "Hello, I love this tutorial!"
		if __name__ == "__main__":
	    	app.run()
		```
2. Install Flask
  1. Install pip:
  		`sudo apt-get install python-pip`
  2. Install virtualenv:
  		`sudo pip install virtualenv`
  3. Set temporary environment:
  		`sudo virtualenv venv`
  4. Enable all permissions for Virtual Environment - sudo doesn't need to be used within it:
  		`sudo chmod -R 777 venv`
  5. Activate virtual environment:
  		`source venv/bin/activate`
  6. Install Flask inside virtual environment:
  		`pip install Flask`
  7. Test if installation was successful and app is running - should display "Running on http://127.0.0.1:5000/":
  		`python __init__.py`
  8. Deactivate the environment:
  		`deactivate`
3. Configure and Enable a new virtual host
  1. Create a virtual host file:
  		`sudo nano /etc/apache2/sites-available/catalog.conf`
  2. Configure the virtual host - paste this into file and change addresses:
  		
  		```
  		<VirtualHost *:80>
				ServerName <PUBLIC IP ADDRESS>
				ServerAdmin admin@<PUBLIC IP ADDRESS>
				WSGIScriptAlias / /var/www/catalog/catalog.wsgi
				<Directory /var/www/catalog/catalog/>
					Order allow,deny
					Allow from all
				</Directory>
				Alias /static /var/www/catalog/catalog/static
				<Directory /var/www/catalog/catalog/static/>
					Order allow,deny
					Allow from all
				</Directory>
				ErrorLog ${APACHE_LOG_DIR}/error.log
				LogLevel warn
				CustomLog ${APACHE_LOG_DIR}/access.log combined
		</VirtualHost>
		```
	3. Enable virtual host:
		`sudo a2ensite catalog`
4. Create the .wsgi file
  1. Move to /var/www/catalog directory and create file called catalog.wsgi:
  		```
  		cd /var/www/catalog
  		sudo nano catalog.wsgi
  		```
  2. Paste following lines into catalog.wsgi file:
  		```
  		#!/usr/bin/python
		import sys
		import logging
		logging.basicConfig(stream=sys.stderr)
		sys.path.insert(0,"/var/www/catalog/")

		from catalog import app as application
		application.secret_key = 'Add your secret key'
  		```
5. Restart Apache - to view output, navigate to your Public IP Address:
	`sudo service apache2 restart`

### 7.b - Stop AH00558 error:
Source: [Tutorial For Linux](http://tutorialforlinux.blogspot.co.uk/2013/10/solve-problem-ah00558-when-restarting.html)

Source: [Ask Ubuntu](http://askubuntu.com/questions/256013/could-not-reliably-determine-the-servers-fully-qualified-domain-name)

1. Open file httpd.conf - by default it will be blank:
	`sudo nano /etc/apache2/httpd.conf`
2. Add following line to the file:
	`ServerName localhost`
3. Open file apache2.conf, and scroll to bottom of file:
	`sudo nano /etc/apache2/apache2.conf`
4. Add following to end of file:
	`ServerName localhost`
5. Save file and restart the server:.
	`sudo service apache2 restart`

### 8 - Install and configure PostgreSQL
Source: [Udacity](https://www.udacity.com/course/viewer#!/c-ud299-nd/l-4340119836/m-4808948548)

#### 8.1 - Do not allow remote connections
1. Install **PostgreSQL**:
	`sudo apt-get install postgresql postgresql-contrib`
2. Default is no remote connections allowed - check this:
	`sudo vim /etc/postgresql/9.3/main/pg_hba.conf`

#### 8.2 - Create new user named catalog with limited permissions to Catalog App database
Source: [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)

1. Create a new user called catalog:
	`sudo adduser catalog`
2. Log into PostgreSQL:
	
	```
	sudo su - postgres
	psql
	```
3. Create user catalog:
	`CREATE ROLE catalog WITH PASSWORD 'DATABASE-PASSWORD';`
4. Grant catalog user database permissions:
	`ALTER USER catalog CREATEDB;`
5. Grant catalog user login permissions:
	`ALTER USER catalog LOGIN`
5. Create a database owned by catalog:
	`CREATE DATABASE catalog WITH OWNER catalog;`

6. Connect to database and lock permissions:
	
	```
	\c catalog
	REVOKE ALL ON SCHEMA public FROM public;
	GRANT ALL ON SCHEMA public TO catalog;
	```
7. Log out of PostgreSQL and postgres user:
	`\q` then `exit`

### 9 - Install git, clone and set up your Catalog App project
Source: [Data Science Bytes](http://www.datasciencebytes.com/bytes/2015/02/24/running-a-flask-app-on-aws-ec2/)

#### 9.1 Install Git
Source: [Github](https://help.github.com/articles/set-up-git/#platform-linux)

1. Install Git:
	`sudo apt-get install git`
2. Set your name for commits:
	`git config --global user.name "YOUR NAME"`
3. Set your email address - same as the one used when signing up for github:
	`git config --global user.email "YOUR EMAIL ADDRESS"`

#### 9.2 - Clone Github repo
Source: [Github](https://help.github.com/articles/cloning-a-repository/)

1. Navigate to `/var/www/catalog/catalog/` directory
	`cd /var/www/catalog/catalog`
2. Clone the Catalog App:
	`git clone https://github.com/simasima121/catalog.git`
3. Move files to `/var/www/catalog/catalog` directory - dont forget .git file:
	`sudo mv <FILENAME> /var/www/catalog/catalog`
4. Remove empty catalog directory:
	`sudo rmdir catalog`

#### 9.3 - Make .git directory inaccessible
Source: [Stack Overflow](http://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible)

1. Navigate to `var/www/catalog`:
	`cd /var/www/catalog`
2. Create file called .htaccess:
	`sudo nano .htaccess`
3. Paste the following into file:
	`RedirectMatch 404 /\.git`

#### 9.4 - Install modules

1. Navigate to `var/www/catalog/catalog`
2. Activate the virtual environment:
	`source venv/bin/activate`
3. Install the http2lib2 in venv:
	`pip install httplib2`
4. Install SQLAlchemy:
	`sudo pip install sqlalchemy`
5. Install oauth2client:
	`sudo pip install --upgrade oauth2client`
6. Install python library:
	`sudo apt-get install libpq-dev python-dev`
6. Install psycopg:
	`sudo apt-get install python-psycopg2`
6. Install psycopg:
	`pip install psycopg2`
7. Install requests:
	`pip install requests`

#### 9.5 - Set up Catalog app

1. In database_setup.py, application.py and userslotsofcategories.py, edit line `engine = create_engine('sqlite:///catalogwithusers.db')` to absolute path - - replace with your *DATABASE-PASSWORD*:
	
	```
	#engine = create_engine('sqlite:///catalogwithusers.db')
	engine = create_engine('postgresql://catalog:catalog@localhost/catalog')
	```
2. Update application.secret_key in catalog_wsgi to same as secret key from application.py:
	`sudo nano ~/catalog.wsgi`
3. Rename application.py:
	`mv application.py __init__.py`

4. Whilst still in virtual environment (if not run `source venv/bin/activate` command), run database_setup.py and userslotsofcategories.py:
	
	```
	python database_setup.py
	python userslotsofcategories.py

	```

#### 9.6 - Checking if app set up properly:
1. Restart Apache:
	`sudo service apache2 restart`
2. Open broswer and put your Public IP address as the url - application should load
3. If you get an internal server error, check apache error files:
	`sudo tail -20 /var/log/apache2/error.log`

#### 9.7 - Set Amazon instance public URL

1. Edit apache config files for web app:
	`sudo nano /etc/apache2/sites-available/catalog.conf`
2. Paste your amazon instance public URL underneath the ServerAdmin line - where X is your instance IP address:
	
	```
	ServerAdmin admin@XX.XX.XXX.X
   ServerAlias HOSTNAME ec2-XX-XX-XXX-X.us-west-2.compute.amazonaws.com
	```
3. Enable the virtual host:
	`sudo a2ensite catalog`

#### 9.8 - Configure OAuth logins:
1. For Google authorization:
  1. Navigate to project in Developer Console: (Developers Console)[https://console.developers.google.com/project]
  2. Navigate to API Manager > Credentials > Click on web client
  3. In Authorizated JavaScript origins add:

  	```
  	http://<Your Public IP Address>
  	ec2-XX-XX-XXX-X.us-west-2.compute.amazonaws.com
  	```
  4. In Authorized redirect URIs add:
  	`ec2-XX-XX-XXX-X.us-west-2.compute.amazonaws.com/oauth2callback`
2. For Facebook authorization:
  1. Navigate to Facebook Develop Site: [Facebook for Developers](https://developers.facebook.com/apps/)
  2. Click on catalog app > Settings > Advanced
  3. In Valid OAuth redirect URIs add:

  	```
  	http://<Your Public IP Address>
  	ec2-XX-XX-XXX-X.us-west-2.compute.amazonaws.com
  	```











