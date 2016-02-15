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
	`sudo apt-get install libapache2-mod-wsgi`
4. Configure apache to handle requsts using WSGI module by editing the `/etc/apache2/sites-enabled/000-default.conf` file:
  1. Add the line at the end of the `<VirtualHost *:80>` block: 
  		`WSGIScriptAlias / /var/www/html/myapp.wsgi`
  2. Restart apache:
  		`sudo apache2ctl restart`

### 7.a - Stop AH00558 error:
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






