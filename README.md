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
