# Continuous Integration and Deployment Automation with Jenkins

## Introduction

**Continuous Integration (CI)** is a software development practice that enables code changes to be frequently integrated and automatically validated. **Jenkins** is an open-source automation server used to automate software development tasks such as building, testing, and deployment.

This project demonstrates the implementation of **Continuous Integration and deployment automation** for the DevOps Tooling Website using Jenkins. Jenkins is integrated with GitHub to automatically retrieve the latest application code and deploy updates to the shared NFS storage.

By automating the deployment process, application updates can be delivered faster and more consistently across the web environment, while reducing repetitive manual tasks and the risk of deployment errors.


## Objectives

The objectives of this project are to:

- Introduce Jenkins CI into the existing Tooling Website infrastructure to automate application deployments.
- Configure Jenkins to integrate with the project's GitHub repository and retrieve the latest application changes.
- Automate the deployment of updated application files to the shared NFS storage.
- Ensure application updates are automatically made available to all web servers behind the Apache Load Balancer.
- Reduce manual deployment tasks and improve the consistency and reliability of application updates.
- Demonstrate how Continuous Integration can improve the efficiency of software delivery in a scalable web environment.

![img1](./Images/img1.png)


# Step 1: Install the Jenkins Server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS
![img2](./Images/img2.png)
![img3](./Images/img3.png)

2. Install JDK (since Jenkins is a Java-based application) 
```bash
sudo apt update
sudo apt install default-jdk-headless
```
![img4](./Images/img4.png)
![img5](./Images/img5.png)

3. Install Jenkins 

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```

![Jenkins install failure with deprecated apt-key method](./Images/img7.png)

> **Note:** `apt-key` is deprecated on modern Ubuntu (especially 24.04), and using it
> resulted in a GPG verification error (`NO_PUBKEY`), which prevented Jenkins from
> being found/installed. The commands below use the current recommended method
> instead, which resolves this issue.

Create the keyrings folder:
```bash
sudo mkdir -p /etc/apt/keyrings
```

Download Jenkins' current signing key into that folder:
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
```

> **Note:** Jenkins' key is rotated periodically and expires roughly every 3 years.
> The key referenced in most guides (`jenkins.io-2023.key`) expired on 26 March 2026,
> which caused a second GPG error even after switching to the `signed-by` method.
> `jenkins.io-2026.key` is the current, valid key at the time of this project.

Create the repo source file, pointing to that key:
```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

Update package lists and install Jenkins:
```bash
sudo apt-get update
sudo apt-get install jenkins -y
```
![img8](./Images/img8.png)
![img9](./Images/img9.png)

Make sure Jenkins is running 
```bash
sudo systemctl status jenkins
```
![img10](./Images/img10.png)

4. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

![img11](./Images/img11.png)

5. Perform initial Jenkins setup.
> Access the jenkins page from the browser http://100.53.133.165:8080/

![img12](./Images/img12.png)
![img13](./Images/img13.png)
![img14](./Images/img14.png)
![img15](./Images/img15.png)
![img16](./Images/img16.png)

# Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks. 

In this part we will configure a simple Jenkins job/project. This job will be triggered by GitHub webhooks and will execute a 'build' task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable webhooks in your GitHub repository settings. 

![img17](./Images/img17.png)

2. Go to Jenkins web console, click “New Item” and create a “Freestyle project”

![img18](./Images/img18.png)

3. To connect the GitHub repository, we will need to provide its URL. 

![img19](./Images/img19.png)

4. In configuration of the Jenkins freestyle project, choose Git repository, we will provide the link to our Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![img20](./Images/img20.png)

Save the configuration and we will run the build. For now we can only do it manually. Click “Build Now” button, the build will be successful and you will see it under #1

![img21](./Images/img21.png)
![img22](./Images/img22.png)

We can see now that the build was successful.
So it was able to clone the repository, fetch the repository, and all the details.
However, the whole idea behind using Jenkins is to make continuous integration a very seamless process.
To achieve this, we have to configure Jenkins to be able to build every time we push a new code to our repository, or update an existing code. 

5. Click Configure our job/project and add these two configurations. 

Configure triggering the job from GitHub webhook and also Configure Post-build Actions to archive all the files - files resulted from a build are called artifacts:

![img23](./Images/img23.png)
![img24](./Images/img24.png)

Now, go ahead and make some change in any file in our GitHub repository (e.g. README.MD file) and push the changes to the main branch.

![img25](./Images/img25.png)

A new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

![img26](./Images/img26.png)
![img27](./Images/img27.png)

Now we configured an automated Jenkins job that receives files from GitHub by webhook trigger this method is considered as push because the changes are being pushed and files transfer is initiated by GitHub. There are also other methods: trigger one job (downstream) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally. 

```bash
sudo ls /var/lib/jenkins/jobs/tooling/builds/2/archive/
```

![img28](./Images/img28.png)

# Step 3: Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are more than 1400 plugins available. now we will need a plugin that is called Publish Over SSH

1. Install Publish Over SSH plugin.

- Navigate to Manage Jenkins from the main dashboard, then select Manage Plugins.

- Go to the Available tab.

- Search for Publish Over SSH in the search bar and proceed with the installation.

![img29](./Images/img29.png)
![img30](./Images/img30.png)

2. Configure the job/project to copy artifacts over to NFS server.

On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

- Provide a private key (content of .pem file that we use to connect to NFS server via SSH/Putty)
- Arbitrary name
- Hostname - can be private IP address of our NFS server
- Username - ec2-user (since NFS server is based on EC2 with RHEL 9)
- Remote directory - /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server

![img31](./Images/img31.png)

Test the configuration and make sure the connection returns Success. TCP port 22 on NFS server must be open to receive SSH connections

![img32](./Images/img32.png)

Save the configuration, open your Jenkins job/project configuration page and add another one Post-build Action. (Send build artifact over ssh).

![img33](./Images/img33.png)

Save the configuration and test it by editing the README.MD file in our GitHub Tooling repository

![img34](./Images/img34.png)

Webhook will trigger a new job and it can be viewed in the Console output. 

![img35](./Images/img35.png)

To make sure that the files in /mnt/apps have been updated – we will connect via SSH to the NFS server and check README.md file
```bash 
cat /mnt/apps/README.md
```

![img36](./Images/img36.png)
![img37](./Images/img37.png)

## Conclusion

This project successfully implemented Continuous Integration and deployment automation using Jenkins for the DevOps Tooling Website. Jenkins was integrated with GitHub using webhooks to automatically retrieve and build application changes, while the resulting artifacts were transferred to the shared NFS storage through SSH.

The implementation demonstrates how Jenkins can automate the application delivery process, reducing manual intervention and ensuring that updates are consistently deployed to the shared web environment.