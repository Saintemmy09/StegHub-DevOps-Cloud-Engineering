# IMPLEMENTATION OF A MERN STACK WEB ENVIRONMENT ON AWS

# Introduction

The MERN Stack is a popular open-source technology stack used to build modern full-stack web applications. It combines a NoSQL database, a backend framework, a frontend library, and a JavaScript runtime, enabling developers to use JavaScript throughout the entire application. MERN stands for the four core technologies outlined below:

- MongoDB – A NoSQL database used to store, organize, and manage application data.
- Express.js – A backend web framework that handles server-side logic, routing, and API requests.
- React – A JavaScript library used to build interactive and dynamic user interfaces.
- Node.js – A JavaScript runtime environment that executes server-side code and powers the backend of the application.

# Step 0: Prerequisites 
1. A t3.micro Amazon EC2 instance running Ubuntu 26.04 LTS was launched in the us-east-1c region using the AWS Management Console.

![Ec2-launch-page](./Images/Ec2-launch-page.png)

# Step 1 - Backend Configuration
Update and upgrade ubuntu package manager

```bash
sudo apt update
sudo apt upgrade -y
```
![Package-manager-update](./Images/Package-manager-update.png)
![Package-manager-upgrade](./Images/Package-manager-upgrade.png)

1. Install Node.js on the server 
```bash 
sudo apt-get install -y node.js
```
![Nodejs-install](./Images/Nodejs-install.png)

2. Verify the node installation
```bash
node -v
```
![Node-confirm](./Images/Node-confirm.png)

#  Application Code Setup
1. Create a new directory for the To-Do project
```bash
mkdir Todo
```  
![Todo-create](./Images/Todo-create.png)    

2. Initialize the project with the command below 
```bash 
npm init
```
![npm-init](./Images/npm-init.png)

3. Install ExpressJS
```bash
npm install express
```
![Expressjs-install](./Images/Expressjs-install.png
)

4. Install dotenv
```bash
npm install dotenv
```
![dotenv-install](./Images/dotenv-install.png)
![indexjs-file](./Images/indexjs-file.png)

5. Start the server 
```bash
node index.js
```
![server-running](./Images/server-running.png)

6. Open it in the browser 
![express-web](./Images/express-web.png)

7. Create a new directory and then a new file 
```bash
mkdir routes
touch api.js
```
![routes-api](./Images/routes-api.png)
![api-file](./Images/api-file.png)

8. Since we need MongoDB (a NoSQL database), There's a need to create a model. Models are central to JavaScript applications and are used to define the database schema — a blueprint that specifies the fields stored in each MongoDB document, including virtual properties (fields not necessarily stored in the database).
To get started, we'll install mongoose, a Node.js package that simplifies working with MongoDB.

```bash
npm install mongoose
```
![mongoose-install](./Images/mongoose-install.png)

9. Create a new folder, cd into it and create a new file. 
```bash 
mkdir models 
touch todo.js
```
![models-todojs](./Images/models-todojs.png)
![todojs-file](./Images/todojs-file.png)

10. Update the api.js file in the routes to use the models, modify the routes to work with the mongodb models created. 
![apijs-file-update](./Images/apijs-file-update.png)

11. We need a database where we will store our data. For this we will make use of mLab. To make our to-db app have access to our mongo-db

![db-create](./Images/db-create.jpeg)

12. In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.

![env-variable](./Images/env-variable.png)

13. There's a need to update the index.js file to reflect the use of our configuration file, which is the .env so node.js can connect to the database

![updated-indexjs](./Images/updated-indexjs.png)

14. Start the server with the command below 
```bash
node index.js
```
![database-connect](./Images/database-connect.png)

# Testing Backend Code without using Frontend using RESTFUL API

1. Testing the database if its working using postman, but an error was returned because the database was not running. 

![Postman-first-error](./Images/Postman-first-error.png)

2. The database was then put on which made postman respond and work adequately 

![Database-activate](./Images/Database-activate.png)
![Postman-first-test](./Images/Postman-first-test.png)

We are now to do a post request 
![Post-request](./Images/Post-request.png)
![Postman-request1](./Images/Postman-request1.png)

Head back to Get 
![Headback-toget](./Images/Headback-toget.png)

Now a delete request 

![Delete-request](./Images/Delete-request.png)

# Step Two - Frontend Creation
1. Create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, use the create-react-app command to scaffold the app.
In the same root directory as your backend code, which is the Todo directory,

```bash
npx create-react-app client
```
![npx-create-react](./Images/npx-react-create.png)

2. Running the React App
Before testing the react app, there are some dependencies that need to be installed.

1. Concurrently — used to run more than one command simultaneously from the same terminal window.
```bash
npm install concurrently --save-dev
```
2. Nodemon — used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
```bash
npm install nodemon --save-dev
```
![concurrent-nodemon-install](./Images/Concurrently-nodemon-install.png)

3. Configure proxy in package.json
```bash
"proxy": "http://localhost:5000"
```
![proxy-packagejson](./Images/proxy-packagejson.png)

3. Start the backend and frontend at the sametime using the command below 
```bash
npm run dev
```
![npm-run-dev](./Images/npm-run-dev.png)
![Default-reactpage](./Images/Default-reactpage.png)

4. Creating React Components 

Inside the components directory, create three files — Input.js, ListTodo.js, and Todo.js:
```bash
touch Input.js ListTodo.js Todo.js
```
![3-js](./Images/3-js.png)

Input.js 
![Input-js](./Images/Input-js.png)

Note: To make use of Axios (a Promise-based HTTP client for the browser and Node.js), you need to cd into your client folder and run:  
```bash
npm install axios 
```
![axios](./Images/axios-install.png)

ListTodo.js

![List-Todo-js](./Images/List-Todo-js.png)

Todo.js 
![Todo-js](./Images/Todo-js.png)

Make little adjustment to the react code in the src directory. 
![adjust-app-js](./Images/adjust-app-js.png)

App.css 
![App-css](./Images/App-css.png)

Index.css 
![index-css](./Images/index-css.png)

In the Todo directory, run 
```bash
npm run dev
```
![npm-run-dev2](./Images/npm-run-dev2.png)

Functional Todo-app 

![functional-todo-app](./Images/functional-todo-app.png)

Conclusion

In this project, I successfully built and deployed a simple To-Do application using the MERN stack. The frontend was developed with React.js, while the backend was built with Express.js and Node.js to handle API requests and application logic. MongoDB served as the database for storing and managing tasks. This project highlight how the MERN stack components work together to build a full-stack web application and reinforced key concepts such as frontend-backend communication, RESTful APIs, and database integration.

