<!-- Headings -->

# IMPLEMENTATION OF A LEMP STACK WEB ENVIRONMENT ON AWS

# Introduction

The LEMP Stack is a popular open-source collection of software used to host and manage websites and web applications. It combines Linux, Nginx, a database system, and PHP scripting to create a reliable environment for serving web content and handling user requests. The name LEMP represents the four core components of the stack, which are outlined below:

- Linux – The operating system that runs the server and provides the foundation for the stack.
- Nginx – The web server responsible for handling client requests and delivering web pages.
- MySQL – The database system used to store, organize, and manage application data.
- PHP – The scripting language used to build the logic and functionality of web applications.

# Step 0: Prerequisites
1.  A t3.micro Amazon EC2 instance running Ubuntu 26.04 LTS was launched in the us-east-1c region using the AWS Management Console.

![Ec2-launch-page](./Images/Ec2-launch-page.png)
![Ec2-launch-page1](./Images/Ec2-launch-page1.png)

2. An SSH key pair named server-key was used to enable secure access to the EC2 instance via port 22.
3. The instance’s security group was configured to allow the following inbound traffic rules:

- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.
- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.

![Instace-sec-group](./Images/Instance-sec-group.png)

4. The instance was launched using the default VPC and subnet for networking configuration.

![Instance-vpc](./Images/Instance-vpc.png)

5. A private SSH key’s permissions were modified, and it was used to connect to the instance by running the following command.

```bash
chmod 400 server-key.pem
```
```bash
ssh -i "server-key.pem" ubuntu@3.91.48.188
```

![Instance-connect](./Images/Instance-connect.png)

# Step 1 - Install Nginx Web Server.

1. Update and upgrade list of packages in package manager  

```bash
sudo apt update
sudo apt upgrade -y
```
![Pack-man-update](./Images/Pack-man-update.png)

2. Install Nginx web server
```bash
sudo apt install nginx
```
![Intall-nginx](./Images/Install-nginx.png)

3. Confirm nginx successfully installed and running. 
```bash
sudo systemctl status nginx
```
![nginx-running](./Images/nginx-running.png)

4. The web server is running and accessed locally within the Ubuntu shell using the command shown below.
```bash
curl http://localhost:80
OR
curl http://3.91.48.188:80
```
![nginx-local](./Images/nginx-local.png)

5. Nginx web server tested on web browser using the public IP address. 

```bash
http://3.91.48.188
```
![nginx-brow-server](./Images/nginx-brow-server.png)

The public IP address can also be retrieved using alternative methods other than the AWS Management Console which requires the modifying the instance metadata options and changing the IMDSv2 from Required to Optional and then run the following command in the terminal.
```bash
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```
![Alter-public-ip](./Images/Alter-public-ip.png)

# Step 2 - Install MySQL.
1. Install MySQL
MySQL is a popular relational database management system used within PHP environments.

```bash
sudo apt install mysql-server
```
![mysql-install](./Images/mysql-install.png)

2. Confirm MySQL is running. 
```bash
sudo systemctl status mysql
```
![mysql-running](./Images/mysql-running.png)

3. Log in to mysql console
```bash
sudo mysql
```
![mysql-console](./Images/mysql-console.png)

4. Run the MySQL secure installation script for improved security. 
```bash
sudo mysql_secure_installation
```
![mysql_secure_installation](./Images/mysql-secure-installation.png)

5. Create a database and user
```bash
CREATE DATABASE testdb;
```
```bash
enter
```
```bash
CREATE USER 'test'@'localhost' IDENTIFIED BY 'Test123@';
```
```bash
enter
```
```bash
GRANT ALL PRIVILEGES ON testdb.* TO 'test'@'localhost';
```
```bash
enter
```
![mysql-data-user-create](./Images/mysql-data-user-create.png)

6. Access mysql using the test user and show database
```bash
mysql -u test -p
```
```bash
enter
```
```bash 
SHOW DATABASES
```
```bash
enter
```
![mysql-testdb-database](./Images/mysql-testdb-database.png)



# Step 3 - Install Php.
1. Install php and all the necessary extensions as well as configuring nginx to use the PHP process and restart nginx to apply these command

```bash
sudo apt install php-fpm php-mysql
```
![php&dep-install](./Images/php&dep-install.png)

2. Confirm php version 
```bash 
php --version
```
![php-version](./Images/php-version.png)

3. Create a directory where websites files will be located inside the /var/www/projectLEMP
```bash
sudo mkdir /var/www/projectLEMP 
```
![web-dir](./Images/web-dir.png)

4. Assign ownership of the directory to the current system user.
```bash
sudo chown -R $USER:$USER /var/www/projectLEMP
```
![owner-projectLEMP](./Images/owner-projectLEMP.png)

5. Open a new configuration file in Nginx's sites-available directory using this command and then add a basic server configuration to the file
```bash
sudo vim /etc/nginx/sites-available/projectLEMP
```
![nginx-sites-available](./Images/nginx-sites-available.png)
![sites-con-file](./Images/sites-con-file.png)

6. Activate your configuration by linking to the config file from Nginx's sites-enabled directory:

```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
![configfile-link](./Images/configfile-link.png)

7. Test configuration for syntax error. 
```bash
sudo nginx -t
``` 
![configfile-test](./Images/configfile-test.png)

8. Disable default nginx host that is currently configured to listen on port 80
```bash
sudo unlink /etc/nginx/sites-enabled/default
```
![disable-default-nginx-host](./Images/disable-default-nginx-host.png)

9. Reload nginx to apply changes 
```bash
sudo systemctl reload nginx
```
10. Verify the setup by creating a simple php file in /var/www/projectLEMP

```bash
sudo vim /var/www/projectLEMP/index.php
```
Add the text below 
```bash
<?php
phpinfo();
?>
```
11. Open browser with the public ip address 
```bash
http://3.91.48.188
```
![php-8-5](./Images/php-8-5-4.png)

Conclusion: 

The LEMP stack was successfully deployed and configured within a Linux-based environment. Nginx, MySQL, and PHP-FPM were integrated to support the hosting of dynamic web applications, while virtual host and PHP processing configurations were tested to ensure proper functionality. The completed setup provides a modern, efficient, and scalable platform for serving web content.

