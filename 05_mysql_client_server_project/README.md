# IMPLEMENT A CLIENT-SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT. 

Client-server architecture is a network model in which two or more computers communicate by exchanging requests and responses over a network. In this model, the computer that initiates a request is called the client, while the computer that processes the request and provides the requested service is called the server. 


# To demonstrate a basic client-server architecture using the MySQL relational database management system (RDBMS), the following steps were executed:

1. Create and configure two Linux-based virtual servers (EC2 instances) on AWS.

![Ec2-instance-launch](./Images/Ec2-instance-launch.png)
![Ec2-instance-launch-page](./Images/Ec2-instance-launch-page1.png)

Establish SSH Connections to the MySQL Server and MySQL Client EC2 instances. 
![2-instance-ssh](./Images/2-instance-ssh.png)

2. Next Step: Install MySQL Server 
 Update the package manager 
```bash
sudo apt update
```
![Apt-update-sqlserver](./Images/Apt-update-sqlserver.png)

- Install MySQL server
```bash 
sudo apt install mysql-server -y
```
![mysql-install](./Images/mysql-install.png)

3. Install the MySQL Client on the client EC2 instance.
```bash
sudo apt update
```
![Apt-update-client](./Images/Apt-update-client.png)

```bash
sudo apt install mysql-client -y
```
![mysql-client-install](./Images/mysql-client-install.png)
![Mysql-server-client-version](./Images/Mysql-server-client-version.png)

4. Configure the MySQL Server Security Group to Allow Inbound Traffic on Port 3306.

Open port 3306 in the MySQL Server security group to allow inbound MySQL traffic.

![Mysql-server-add-port](./Images/Mysql-server-add-port.png)
![Mysql-server-add-port1](./Images/Mysql-server-add-port1.png)

5. Configure the MySQL Server to Allow Connections from the MySQL Client EC2 Instance.

```bash
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
![Config-command](./Images/Config-command.png)
![Sql-configuration-file](./Images/Sql-configuration-file.png)
 
6. Restart MySQL 
 ```bash
 sudo systemctl restart mysql
 ```
 Verify MySQL is listening on all interfaces
 ```bash
 sudo ss -tlnp | grep 3306
 ```
 ![Sql-listening-interface](./Images/Sql-listening-interface.png)

7. Create a MySQL User with Remote Access Privileges.

MySQL is now listening on all network interfaces. Since the root user cannot typically log in remotely for security reasons, create a dedicated user that is permitted to connect from the MySQL Client EC2 instance.
```bash
sudo mysql
```
```bash
CREATE USER 'client_saintemmy'@'172.31.23.152' IDENTIFIED BY '!';
GRANT ALL PRIVILEGES ON *.* TO 'client_saintemmy'@'172.31.23.152';
FLUSH PRIVILEGES;
EXIT;
```

![Sql-user-create1](./Images/Sql-user-create1.png)

Connect from mysql client (172.31.23.152)
```bash
mysql -h 172.31.21.201 -u client_saintemmy -p
```
![Client_saintemmy_connect1](./Images/client_saintemmy_connect1.png)

Conclusion: 

This project successfully demonstrated a basic client-server architecture using MySQL on two Amazon EC2 instances. After configuring the MySQL server for remote access and creating the appropriate user permissions, the MySQL Client EC2 instance was able to connect to and communicate with the MySQL Server, confirming that the client-server setup functioned as expected.