# Tooling Website deployment automation with Continuous Integration: Introduction to Jenkins

The aim of this project is to automate routine tasks using a free and open-source automation server -Jenkins. According to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In this project, Jenkins CI capabilities would be utilized to make changes to the source code in Github https://github.com/<yourname>/tooling which will automatically be updated to the Tooling Website.

## Tasks

This project will enhance the architecture prepared in Project 8 by adding a Jenkins server, Configure a job to automatically deploy source codes and changes from Git to NFS server. Below is the updated Project architecture 



## INSTALL JENKINS
- Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it “Jenkins”
- Install JDK (since Jenkins is a Java-based application)

> sudo apt update 

> sudo apt install default-jdk-headless

 - Install Jenkins

> wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

> sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
    
> sudo apt update

> sudo apt-get install jenkins

- Check Status of Jenkins to ensure it is up and running
- Create a new inbound rule in the Jenkins server security group to open TCP port 8080
- Start initial Jenkins setup by accessing the Jenkins client side from a browser using
http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080
- You will be prompted to provide a default admin password. Retrieve it from your server:
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

- Select the prompt to install suggested plugins when prompted to choose plugins to install
Once plugins installation is done - create an admin user Then the installation is completed!

## 2. Configure Jenkins to retrieve source codes from GitHub using Webhooks

- Enable webhooks in the GitHub repository settings
- Go to Jenkins web console, click “New Item” and create a “Freestyle project”
- Connect the GitHub repository, by providing its URL which can be copied from the repository 
In configuration of the Jenkins freestyle project choose Git repository, provide the link to the Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.
- Save the configuration and try to run the build. For now we can only do it manually. Click “Build Now” button. If the build is successful, it will be seen under build #1
- Open the build and check in “Console Output” if it has run successfully.
*Note: This build does not produce anything and it runs only when we trigger it manually.

- To trigger builds automatically, Click “Configure” the job/project and add these two configurations
        Configure triggering the job from GitHub webhook:
  Configure “Post-build Actions” to archive all the files - files resulted from a build are called “artifacts”.

- Make some change in any file in the GitHub repository (e.g. README.MD file) and push the changes to the master branch. You will see that a new build has been launched automatically (by webhook) and you can see its results - artifacts, saved on Jenkins server

**An automated Jenkins job that receives files from GitHub by webhook trigger has now been configured (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.**

- By default, the artifacts are stored on Jenkins server locally

> ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/

## 3. Configure Jenkins to copy files to NFS server via SSH

The next step is to copy them to our NFS server to /mnt/apps directory.

**Install “Publish Over SSH” plugin.

- On main dashboard select “Manage Jenkins” and choose “Manage Plugins” menu item.

- On “Available” tab search for “Publish Over SSH” plugin and install it

Configure the job/project to copy artifacts over to NFS server.

- On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

- Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

>Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
>Arbitrary name
>Hostname - can be private IP address of your NFS server
>Username - ec2-user (since NFS server is based on EC2 with RHEL 8)
>Remote directory - /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
- Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.


- Save the configuration, open your Jenkins job/project configuration page and add another one “Post-build Action”
Configure it to send all files probuced by the build into our previouslys define remote directory. In our case we want to copy all files and directories - so we use **.
Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.Webhook will trigger a new job and in the “Console Output” of the job you will find something like this:
To make sure that the files in /mnt/apps have been udated - connect via SSH/Putty to your NFS server and check README.MD file  cat /mnt/apps/README.md

# BLOCKERS
Error
Solution: Changed ownership on webserver servers

# RESOURCES
1. https://darey.io
2. https://en.m.wikipedia.org/wiki/Webhook
3. https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/




