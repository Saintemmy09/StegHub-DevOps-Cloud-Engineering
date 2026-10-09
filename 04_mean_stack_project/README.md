# IMPLEMENTATION OF MEAN STACK TECHNOLOGY ON AWS 

# Introduction

The MEAN Stack is a full-stack JavaScript technology stack designed for building dynamic, scalable, and data-driven web applications. By using JavaScript across both the client and server, it streamlines the development process and enables seamless communication between application components. The acronym MEAN represents the four technologies that makes up the stack:

- MongoDB – A document-oriented NoSQL database that stores application data in flexible, JSON-like documents.
- Express.js – A framework for Node.js that helps developers build the part of a web application that processes requests, performs tasks, and sends information back to users.
- Angular – A powerful frontend framework developed by Google for building robust, component-based, single-page applications.
- Node.js – A cross-platform JavaScript runtime that executes server-side code and provides the foundation for backend services.

# Step 0: Prerequisites 
1. A t3.micro Amazon EC2 instance running Ubuntu 26.04 LTS was launched in the us-east-1

![Ec2-launch-page](./Images/Ec2-launch-page2.png)

# Step 1: Install NodeJS

Update Ubuntu Packages.

```bash
sudo apt update 
```
![Package-update](./Images/Package-update.png)

Add certificates 

```bash 
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_22.x | sudo -E bash -
```

![Add-cert1](./Images/Add-cert1.png)
![Add-cert2](./Images/Add-cert2.png)


1. Install Node.js on the server 
```bash 
sudo apt install -y nodejs
```
![Nodejs-install](./Images/Nodejs-install.png)

Install npm package manager 
```bash
sudo apt install npm
```

# Step 2: Install MongoDB

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

Import MondgoDB repository key: 
```bash
sudo apt-get install -y gnupg curl
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
    sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
    --dearmor
```

  Add MongoDB repository package:

```bash
   echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```
![Db-repokey-repopack](./Images/Db-repokey-repopack.png)

Install MongoDB

```bash
sudo apt-get install -y mongodb-org
```
![MongoDB-install](./Images/MongoDB-install.png)

Start the server 

Check the status of server 
```bash
sudo systemctl status mongod
```
![MongoDB-inactive](./Images/MongoDB-inactive.png)

The server is inactive and needs to be started. 
```bash
sudo systemctl start mongod
```
Check the server again if its now up and running
```bash
sudo systemctl status mongod
```
![MongoDB-install](./Images/MongoDB-active.png)

Install npm, node package manager
```bash
sudo apt install -y npm
```
Install body-parser package
We need a body-parser package to help us process JSON files passed in request to server.
```bash
sudo npm install body-parser
```
![Body-parser-install](./Images/Body-parser-install.png)

Create a new directory named "Books", and initialize the npm project.

```bash
mkdir Books && cd Books
```
![New-dir-cd](./Images/New-dir-cd.png)

```bash
npm init
```
![npm-init](./Images/npm-init.png)

Create a file "server.js" 
```bash
vi server.js
```
![server-js](./Images/server-js.png)

# Step 3: Install Express and set up routes to the server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.

Install ExpressJS

```bash
sudo npm install express 
sudo npm install mongoose
```
![Express-mongoose-install](./Images/Express-mongoose-install.png)

Create a new folder named "apps"
```bash 
mkdir apps
cd apps
vi routes.js
```
![apps-routes-create](./Images/apps-routes-create.png)
![routesjs](./Images/routesjs.png)

Create another folder named "models'
```bash
mkdir models 
vi book.js
```
![bookjs](./Images/bookjs.png)

# Step 4: Access the routes with AngularJS

 AngularJS will be used to connect our web page with Express and perform actions on our book register.

Create a new folder named "public"
```bash
mkdir public
cd mkdir
vi script.js
```
![Public-scriptjs](./Images/Public-scriptjs.png)
![Scriptjs](./Images/Scriptjs.png)

Create a new file named "index-html"

```bash
vi index.html
```
![index-html](./Images/index-html1.png)

Start the server 
```bash 
node server.js
```
![server-start](./Images/server-start.png)
![Outcome1](./Images/Outcome1.png)
![Outcome2](./Images/Outcome2.png)


Conclusion:

In this project, I successfully built and deployed a simple Book Management application using the MEAN stack. The backend was developed with Node.js and Express.js to provide RESTful API endpoints, while MongoDB, together with Mongoose, was used to store and manage book records. Angular was used to build the frontend and communicate with the backend through HTTP requests. This project reinforced my understanding of full-stack application development, RESTful APIs, frontend-backend communication, database integration, and implementing CRUD operations using the MEAN stack.
