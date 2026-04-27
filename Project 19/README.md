## Automate Infrastructure With IaC using Terraform. Part 4 – Terraform Cloud

### What Terraform Cloud is and why use it
By now, you should be pretty comfortable writing Terraform code to provision Cloud infrastructure using [Configuration Language (HCL)](https://www.terraform.io/docs/language/). Terraform is an open-source system, that you installed and ran a Virtual Machine (VM) that you had to create, maintain and keep up to date. In Cloud world it is quite common to provide a managed version of an open-source software. Managed means that you do not have to install, configure and maintain it yourself – you just create an account and use it "as A Service".

[Terraform Cloud](https://www.terraform.io/cloud) is a managed service that provides you with Terraform CLI to provision infrastructure, either on demand or in response to various events.

By default, Terraform CLI performs operation on the server whene it is invoked, it is perfectly fine if you have a dedicated role who can launch it, but if you have a team who works with Terraform – you need a consistent remote environment with remote workflow and shared state to run Terraform commands.

Terraform Cloud executes Terraform commands on disposable virtual machines, this remote execution is also called [remote operations](https://www.terraform.io/docs/language/).

### Migrate your `.tf` codes to Terraform Cloud
Let's explore how we can migrate our codes to Terraform Cloud and manage our AWS infrastructure from there:
1.	**Create a Terraform Cloud account**

Follow [this link](https://app.terraform.io/signup/account), create a new account, verify your email and you are ready to start
 
Most of the features are free, but if you want to explore the difference between free and paid plans – you can check it on [this page](https://www.hashicorp.com/products/terraform/pricing).

![Task19](./Images/Task%2019.1.png)
![Task19](./Images/Task%2019.2.png)

2.	**Create an organization**

Select "Start from scratch", choose a name for your organization and create it.

![Task19](./Images/Task%2019.3.png)

3.	**Configure a workspace**

- Before we begin to configure our workspace – watch [this part of the video](https://youtu.be/m3PlM4erixY?t=287) to better understand the difference between **version control workflow**, **CLI-driven workflow** and **API-driven workflow** and other configurations that we are going to implement.

- We will use **version control workflow** as the most common and recommended way to run Terraform commands triggered from our git repository.


- Create a new repository in your GitHub and call it **terraform-cloud**, push your Terraform codes developed in the previous projects to the repository.

- Choose **version control workflow** and you will be promped to connect your GitHub account to your workspace – follow the prompt and add your newly created repository to the workspace.
 ![Task19](./Images/Task%2019.4.png)
 ![Task19](./Images/Task%2019.5.png)
 ![Task19](./Images/Task%2019.6.png)

 
- Move on to "Configure settings", provide a description for your workspace and leave all the rest settings default, click "Create workspace".

4.	**Configure variables**
Terraform Cloud supports two types of variables: environment variables and Terraform variables. Either type can be marked as sensitive, which prevents them from being displayed in the Terraform Cloud web UI and makes them write-only.


![Task19](./Images/Task%2019.7.1.png)
![Task19](./Images/Task%2019.7.png)

- Set two environment variables: **AWS_ACCESS_KEY_ID** and **AWS_SECRET_ACCESS_KEY**, set the values that you used in Project 16. These credentials will be used to privision your AWS infrastructure by Terraform Cloud.

![Task19](./Images/Task%2019.8.png)

- After you have set these 2 environment variables – your Terraform Cloud is all set to apply the codes from GitHub and create all necessary AWS resources.

5.	Now it is time to run our Terrafrom scripts, but in our previous project which was project 18, we talked about using Packer to build our images, and Ansible to configure the infrastructure, so for that we are going to make few changes to our our existing [respository](https://github.com/BosunEnoch7/terraform-cloud) from Project 18.

The files that would be Addedd is;
- **AMI**: for building packer images
- **Ansible**: for Ansible scripts to configure the infrastucture

Before you proceed ensure you have the following tools installed on your local machine;
- [packer](https://learn.hashicorp.com/tutorials/packer/get-started-install-cli)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

![Task19](./Images/Task%2019.9.png)
![Task19](./Images/Task%2019.10.png)
![Task19](./Images/Task%2019.11.png)
![Task19](./Images/Task%2019.12.png)

Refer to this [repository](https://github.com/BosunEnoch7/terraform-cloud) for guidiance on how to refactor your enviroment to meet the new changes above and ensure you go through the `README.md file`.

![Task19](./Images/Task%2019.13.png)

6.	**Run terraform plan and terraform apply from web console**

Switch to "Runs" tab and click on "Queue plan manualy" button. If planning has been successfull, you can proceed and confirm Apply – press "Confirm and apply", provide a comment and "Confirm plan"

Check the logs and verify that everything has run correctly. Note that Terraform Cloud has generated a unique state version that you can open and see the codes applied and the changes made since the last run.

![Task19](./Images/Task%2019.14.png)

Here, I encounterd this plan error
![Task19](./Images/Task%2019.15.png)

Then I fix
![Task19](./Images/Task%2019.16.png)
![Task19](./Images/Task%2019.17.png)
![Task19](./Images/Task%2019.18.png)

Let's Re Run
![Task19](./Images/Task%2019.19.png)
![Task19](./Images/Task%2019.20.png)
![Task19](./Images/Task%2019.21.png)

Let's confirm created resources in AWS console
![Task19](./Images/Task%2019.22.png)
![Task19](./Images/Task%2019.23.png)

Run Destroy before next step
![Task19](./Images/Task%2019.24.1.png)


7.	**Test automated terraform plan**

By now, you have tried to launch plan and apply manually from Terraform Cloud web console. But since we have an integration with GitHub, the process can be triggered automatically. Try to change something in any of `.tf` files and look at "Runs" tab again – **plan** must be launched automatically, but to **apply** you still need to approve manually. Since provisioning of new Cloud resources might incur significant costs. Even though you can configure "Auto apply", it is always a good idea to verify your **plan** results before pushing it to *apply* to avoid any misconfigurations that can cause ‘bill shock’.

![Task19](./Images/Task%2019.24.png)

`Push` to github
![Task19](./Images/Task%2019.24.2.png)
![Task19](./Images/Task%2019.25.png)
![Task19](./Images/Task%2019.26.png)


**Note:** First, try to approach this projectoun your own, but if you hit any blocker and could not move forward with the project, refer to [this video](https://www.youtube.com/watch?v=nCemvjcKuIA&feature=youtu.be)

### Congratulations!