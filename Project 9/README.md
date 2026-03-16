# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

In previous Project 8, we introduced the horizontal scalability concept, which allows us to add new Web Servers to our Tooling Website. You have successfully deployed a set-up with 2 Web Servers and a Load Balancer to distribute traffic between them. If it is just two or three servers – it is not a big deal to configure them manually. Imagine that you would need to repeat the same task repeatedly adding dozens or even hundreds of servers.

DevOps is about Agility and the speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is the Automation of routine tasks.

In this project, we are going to start automating part of our routine tasks with a free and open-source automation server – [Jenkins](https://en.wikipedia.org/wiki/Jenkins_(software)). It is one of the most popular [CI/CD](https://en.wikipedia.org/wiki/CI/CD) tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named "Hudson".

According to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.
In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub ``https://github.com/<yourname>/tooling`` will be automatically be updated to the Tooling Website.

## Side Self Study
Read about [Continuous Integration, Continuous Delivery and Continuous Deployment](https://circleci.com/continuous-integration/).

## Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, and configuring a job to automatically deploy source code changes from Git to the NFS server.

Here is what your updated architecture will look like upon completion of this project:
![Task9](./Images/Task%209.05.png)


## INSTALL AND CONFIGURE JENKINS SERVER
**Step 1 – Install the Jenkins server**
1.	Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"
2.	Install JDK (since Jenkins is a Java-based application)
```
sudo apt update
sudo apt install default-jdk-headless
```
![Task9](./Images/Task%209.1.png)

3.	Install Jenkins
```
sudo rm -f /etc/apt/sources.list.d/jenkins.list
sudo rm -f /etc/apt/keyrings/jenkins-keyring.asc

sudo apt update
sudo apt install -y fontconfig openjdk-17-jre

sudo mkdir -p /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install -y jenkins
sudo systemctl enable --now jenkins
sudo systemctl status jenkins --no-pager
```
![Task9](./Images/Task%209.2.png)
![Task9](./Images/Task%209.3.png)

4.	By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group
![Task9](./Images/Task%209.4.png)

5.	Perform initial Jenkins setup.
From your browser access ```http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080```
You will be prompted to provide a default admin password


![Task9](./Images/Task%209.40.png)

Retrieve it from your server:
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
![Task9](./Images/Task%209.6.png)
Then you will be asked which plugings to install – choose suggested plugins.
 
Once plugin installation is done – create an admin user and you will get your Jenkins server address.
![Task9](./Images/Task%209.7.png)
![Task9](./Images/Task%209.8.png)
The installation is completed!
 
**Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks**

In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will be triggered by GitHub [webhooks](https://en.wikipedia.org/wiki/Webhook) and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.
1.	Enable webhooks in your GitHub repository settings
 ![Task9](./Images/Task%209.9.png)
 ![Task9](./Images/Task%209.10.png)
 ![Task9](./Images/Task%209.11.png)
 ![Task9](./Images/Task%209.12.png)
 ![Task9](./Images/Task%209.13.png)

2.	Go to Jenkins web console, click "New Item" and create a "Freestyle project"
 ![Task9](./Images/Task%209.14.png)
 ![Task9](./Images/Task%209.15.png)
To connect your GitHub repository, you will need to provide its URL, you can copy it from the repository itself
 ![Task9](./Images/Task%209.16.png)
In the configuration of your Jenkins freestyle project choose Git repository, and provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.
 ![Task9](./Images/Task%209.17.png)
 ![Task9](./Images/Task%209.18.png)
 ![Task9](./Images/Task%209.19.png)
Save the configuration and let us try to run the build. For now, we can only do it manually.
Click the "Build Now" button, if you have configured everything correctly, the build will be successful and you will see it under **#1**
![Task9](./Images/Task%209.20.png)
You can open the build and check in "Console Output" if it has run successfully.
![Task9](./Images/Task%209.21.png)
If so – congratulations! You have just made your very first Jenkins build!
But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

3. Click "Configure" your job/project and add these two configurations
Configure triggering the job from the GitHub webhook:
 ![Task9](./Images/Task%209.22.png)
Configure "Post-build Actions" to archive all the files – files resulting from a build are called "artifacts".
 ![Task9](./Images/Task%209.23.png)
Now, go ahead and make some changes in any file in your GitHub repository (e.g. *README.md* file) and push the changes to the master branch.
You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on the Jenkins server.
 ![Task9](./Images/Task%209.24.png)

You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and file transfer is initiated by GitHub). There are also other methods: trigger one job (downstream) from another (upstream), poll GitHub periodically and others.
By default, the artifacts are stored on the Jenkins server locally
```
ls /var/lib/jenkins/jobs/<Your_Job_Name>/builds/<build_number>/archive/
```
![Task9](./Images/Task%209.25.png)


## CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH
**Step 3 – Configure Jenkins to copy files to NFS server via SSH**

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to `/mnt/apps` directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called ["Publish Over SSH"](https://plugins.jenkins.io/publish-over-ssh/).
1.	Install the "Publish Over SSH" plugin.
On the main dashboard select "Manage Jenkins" and choose the "Manage Plugins" menu item.

On the "Available" tab search for the "Publish Over SSH" plugin and install it
![Task9](./Images/Task%209.26.png)
![Task9](./Images/Task%209.27.png)
![Task9](./Images/Task%209.28.png)

2.	Configure the job/project to copy artifacts over to the NFS server.

On the main dashboard select "Manage Jenkins" and choose the "Configure System" menu item.
Scroll down to Publish over the SSH plugin configuration section and configure it to be able to connect to your NFS server:
1.	Provide a private key (the content of .pem file that you use to connect to the NFS server via SSH/Putty)
![Task9](./Images/Task%209.29.png)
2.	**Arbitrary name**
3.	**Hostname**– can be `private IP address` of your NFS server
4.	Username – `ec2-user` (since the NFS server is based on EC2 with RHEL 9)
5.	Remote directory – `/mnt/apps` since our Web Servers use it as a mounting point to retrieve files from the NFS server

Test the configuration and make sure the connection returns **Success**. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.
 ![Task9](./Images/Task%209.30.png)
Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action"
 
Configure it to send all files produced by the build into our previously define remote directory. In our case we want to copy all files and directories – so we use **.
![Task9](./Images/Task%209.31.png)
![Task9](./Images/Task%209.32.png)
Save this configuration and go ahead, and change something in ***README.MD*** file in your GitHub Tooling repository.
Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:
```
Finished: SUCCESS
```
![Task9](./Images/Task%209.33.png)
In my case, It shows **UNSTABLE**,

The issue is; My pipeline is already working up to the transfer stage. The only remaining issue is that
ec2-user does not have permission to write into `/mnt/apps`
but here is how you should troubleshoot it.
- Run these on the NFS server:
```
sudo chown -R ec2-user:ec2-user /mnt/apps
sudo chmod -R 755 /mnt/apps
``` 
![Task9](./Images/Task%209.34.png)
- Then push a small change/changes again.
![Task9](./Images/Task%209.35.png)

To make sure that the files in /mnt/apps have been updated – connect via SSH/Putty to your NFS server and check ***README.MD*** file
```
cat /mnt/apps/README.md | grep "your exact changes"
```
I used the grep command to filter the exact changes/text I updated the ***README.md*** file
![Task9](./Images/Task%209.36.png)

If you see the changes you had previously made in your GitHub like mine – the job works as expected.
**Congratulations!**

 