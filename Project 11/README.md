## ANSIBLE CONFIGURATION MANAGEMENT


In Projects 7 to 10 you had to perform a lot of manual operations to seet up virtual servers, install and configure required software, deploy your web application.

This Project will make you appreciate DevOps tools even more by making most of the routine tasks automated with [Ansible](https://en.wikipedia.org/wiki/Ansible_(software)) [Configuration Management](https://www.redhat.com/en/topics/automation/what-is-configuration-management), at the same time you will become confident at writing code using declarative language such as [YAML](https://en.wikipedia.org/wiki/YAML).

Let us get started!

## Ansible Client as a Jump Server (Bastion Host)

A [Jump Server](https://en.wikipedia.org/wiki/Jump_server) (sometimes also referred as [Bastion Host](https://en.wikipedia.org/wiki/Bastion_host)) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces [attack surface](https://en.wikipedia.org/wiki/Attack_surface).

On the diagram below the Virtual Private Network (VPC) is divided into [two subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html) – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.
![Task11](./Images/Task%2011.05.png)

### Task
- Install and configure Ansible client to act as a Jump Server/Bastion Host
- Create a simple Ansible playbook to automate servers configuration

### INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE
1.	Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.

2.	In your GitHub account create a new repository and name it ansible-config-mgt.

![Task11](./Images/Task%2011.1.png)
![Task11](./Images/Task%2011.2.png)
3.	Install Ansible
```
sudo apt update

sudo apt install ansible
```
![Task11](./Images/Task%2011.3.png)

Check your Ansible version by running ansible --version
 
 ![Task11](./Images/Task%2011.4.png)

4.	Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.

- Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.
- Configure Webhook in GitHub and set webhook to trigger ansible build.
- Configure a Post-build job to save all `(**)` files, like you did it in Project 9.

![Task11](./Images/Task%2011.5.png)
![Task11](./Images/Task%2011.6.png)
![Task11](./Images/Task%2011.7.png)
![Task11](./Images/Task%2011.8.png)
![Task11](./Images/Task%2011.9.png)

5.	Test your setup by making some change in *README.MD* file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder
```
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
```

**Note:** Trigger Jenkins project execution only for /main (master) branch.

![Task11](./Images/Task%2011.10.png)
![Task11](./Images/Task%2011.11.png)

Now your setup will look like this:
![Task11](./Images/Task%2011.06.png)

**Tip**: Every time you stop/start your Jenkins-Ansible server – you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an [Elastic IP](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) to your Jenkins-Ansible server (you have done it before to your LB server in Project 10). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

![Task11](./Images/Task%2011.12.png)
![Task11](./Images/Task%2011.13.png)
### Step 2 – Prepare your development environment using Visual Studio Code

6. First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an [Integrated development environment (IDE)](https://en.wikipedia.org/wiki/Integrated_development_environment) or [Source-code Editor](https://en.wikipedia.org/wiki/Source-code_editor). There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – [Visual Studio Code (VSC)](https://en.wikipedia.org/wiki/Visual_Studio_Code), you can get it [here](https://code.visualstudio.com/download).

7.	After you have successfully installed VSC, configure it to [connect to your newly created GitHub repository](https://www.darey.io/docs/install-and-configure-ansible-on-ec2-instance/www.youtube.com/watch?v=3Tn58KQvWtU&&t).

8.	Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance
9.	``git clone <ansible-config-mgt repo link>
``

![Task11](./Images/Task%2011.14.png)

### BEGIN ANSIBLE DEVELOPMENT

10.	In your *`ansible-config-mgt`* GitHub repository, create a new branch that will be used for development of a new feature.

**Tip:** Give your branches descriptive and comprehensive names, for example, if you use [Jira](https://www.atlassian.com/software/jira) or [Trello](https://trello.com/) as a project management tool – include ticket number (e.g. PRJ-145) in the
 name of your branch and add a topic and a brief description what this branch is about – a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)

 ![Task11](./Images/Task%2011.15.png)
 ![Task11](./Images/Task%2011.16.png)

11.	Checkout the newly created feature branch to your local machine and start building your code and directory structure
12.	Create a directory and name it playbooks – it will be used to store all your playbook files.
![Task11](./Images/Task%2011.17.png)

13.	Create a directory and name it inventory – it will be used to keep your hosts organised.
14.	Within the playbooks folder, create your first playbook, and name it *common.yml*
15.	Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) *dev, staging, uat, and prod* respectively.
![Task11](./Images/Task%2011.18.png)

### Step 4 – Set up an Ansible Inventory
An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

**Note:** Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of [ssh-agent](https://smallstep.com/blog/ssh-agent-explained/). Now you need to import your key into ssh-agent:

To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video:
- For Windows users – [ssh-agent on windows](https://www.youtube.com/watch?v=OplGrY74qog)
- For Linux users – [ssh-agent on linux](https://www.youtube.com/watch?v=OplGrY74qog&feature=youtu.be)
```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

Confirm the key has been added with the command below, you should see the name of your key

`ssh-add -l`

Now, ssh into your Jenkins-Ansible server using ssh-agent
```
ssh -A ubuntu@public-ip
```
![Task11](./Images/Task%2011.19.png)
![Task11](./Images/Task%2011.20.png)
![Task11](./Images/Task%2011.21.png)
![Task11](./Images/Task%2011.22.png)
![Task11](./Images/Task%2011.23.png)
![Task11](./Images/Task%2011.24.png)
![Task11](./Images/Task%2011.25.png)
![Task11](./Images/Task%2011.26.png)

Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.
Update your inventory/dev.yml file with this snippet of code:
```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```

## CREATE A COMMON PLAYBOOK

### Step 5 – Create a Common Playbook
It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in *inventory/dev.*

In *common.yml* playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your *playbooks/common.yml* file with following code:
```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```
![Task11](./Images/Task%2011.27.png)
Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install [wireshark](https://en.wikipedia.org/wiki/Wireshark) utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses `root `user to perform this task and respective package manager: `yum` for RHEL 8 and `apt` for Ubuntu.
Feel free to update this playbook with following tasks:

- Create a directory and a file inside it
- Change timezone on all servers
- Run some shell script
- …

For a better understanding of Ansible playbooks – watch this [video](youtube.com/watch?v=ZAdJ7CdN7DY&feature=youtu.be) from RedHat and read this [article](https://www.redhat.com/en/topics/automation/what-is-an-ansible-playbook).

### Step 6 – Update GIT with the latest code
Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.
In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".
Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.
Commit your code into GitHub:
16.	use git commands to add, commit and push your branch to GitHub.
```
git status

git add <selected files>

git commit -m "commit message"
```

17.	Create a Pull request (PR)

18.	Wear a hat of another developer for a second, and act as a reviewer.
19.	If the reviewer is happy with your new feature development, merge the code to the master branch.
![Task11](./Images/Task%2011.28.png)
20.	Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.
![Task11](./Images/Task%2011.29.png)
![Task11](./Images/Task%2011.30.png)

Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to `/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/` directory on Jenkins-Ansible server.
![Task11](./Images/Task%2011.31.png)

## RUN FIRST ANSIBLE TEST
### Step 7 – Run first Ansible test
Now, it is time to execute ansible-playbook command and verify if your playbook actually works:
```
cd ansible-config-mgtansible-playbook -i inventory/dev.yml playbooks/common.yml
```
![Task11](./Images/Task%2011.32.png)
This the error encountered. 
### TroubleShooting
- I ran these commads as indicated
![Task11](./Images/Task%2011.34.png)
![Task11](./Images/Task%2011.35.png)
- I then make some edits on the *`inventory/dev.yml`* as shown below
![Task11](./Images/Task%2011.36.png)

- Since the changes was made locally, I git push and git pull to main branch
![Task11](./Images/Task%2011.37.png)
![Task11](./Images/Task%2011.38.png)
![Task11](./Images/Task%2011.39.png)
![Task11](./Images/Task%2011.40.png)
- After all the troubleshooting, I hitted this again

![Task11](./Images/Task%2011.42.png)

- Then I restructure/make some edits to *`common.yml`*
![Task11](./Images/Task%2011.43.png)
- Then another buid was made by jenkins after the changes is been push, PR, Merge and Pull
![Task11](./Images/Task%2011.44.png)
- Then I reran these command as shown in the image below
![Task11](./Images/Task%2011.45.png)
![Task11](./Images/Task%2011.46.png)

You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version

![Task11](./Images/Task%2011.47.png)
![Task11](./Images/Task%2011.48.png)

Your updated with Ansible architecture now looks like this:
![Task11](./Images/Task%2011.07.png)
### Optional step – Repeat once again
Update your ansible playbook with some new Ansible tasks and go through the full checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook cycle again to see how easily you can manage a servers fleet of any size with just one command!

### Congratulations
You have just automated your routine tasks by implementing your first Ansible project! There is more exciting projects ahead, so lets keep it moving!
 




