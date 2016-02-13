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

### 6 - 



