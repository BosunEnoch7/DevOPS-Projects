# AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY

### General Overview
In previous projects you used 
basic [Infrastructure as a Service (IaaS)](https://en.wikipedia.org/wiki/Infrastructure_as_a_service) offerings from AWS such as [EC2 (Elastic Compute 
Cloud)](https://en.wikipedia.org/wiki/Amazon_Elastic_Compute_Cloud) as rented Virtual Machines and [EBS (Elastic Block Store)](https://en.wikipedia.org/wiki/Amazon_Web_Services#Amazon_Elastic_Block_Store_(EBS)), you have also learned how to 
configure [Key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) and basic [Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html). 

But the power of Clouds is not only in being able to rent Virtual Machines – it is much more than 
that. From now on, you will start gradually study different Cloud concepts and tools on example 
of AWS, but do not be worried, your knowledge will not be limited to only AWS specific 
concepts – overall principles are common across most of the major Cloud Providers 
(e.g., Microsoft Azure and Google Cloud Platform). 

***NOTE:*** The next few projects will be implemented manually. Before you begin to automate 
infrastructure in the cloud, it is very important that you can build the solution manually. 
Otherwise, programming your automation may become frustrating very quickly. 

You will build a secure infrastructure inside AWS [VPC (Virtual Private Cloud)](https://en.wikipedia.org/wiki/Amazon_Virtual_Private_Cloud) network for a 
fictitious company (Choose an interesting name for it) that uses WordPress CMS for its main 
business website, and a **Tooling Website** (`https://github.com/BosunEnoch7/tooling`) for 
their DevOps team. As part of the company’s desire for improved security and performance, a 
decision has been made to use a [reverse proxy technology from **NGINX**](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/) to achieve this. 
Cost, Security, and Scalability are the major requirements for this project. Hence, implementing 
the architecture designed below, ensure that infrastructure for both websites, WordPress and 
Tooling, is resilient to Web Server’s failures, can accomodate to increased traffic and, at the same 
time, has reasonable cost.

![Task15](./Images/architecture.png)

## Starting Off Your AWS Cloud Project 

### There are few requirements that must be met before you begin:

1. Properly configure your AWS account and Organization Unit [Watch How To Do This Here](https://www.youtube.com/watch?v=9PQYCc_20-Q&feature=youtu.be)
    - Create an AWS [Master account](https://aws.amazon.com/free/). (*Also known as Root Account*)
    - Within the Root account, create a sub-account and name it DevOps. (You will need another email address to complete this)
    
    ![Task15](./Images/Task%2015.1.png)
    ![Task15](./Images/Task%2015.2.png)
    ![Task15](./Images/Task%2015.3.png)
    ![Task15](./Images/Task%2015.4.png)
    ![Task15](./Images/Task%2015.5.png)

    - Within the Root account, create an **AWS Organization Unit (OU)**. Name it **Dev**. (We will launch Dev resources in there).

   ![Task15](./Images/Task%2015.6.png)
   ![Task15](./Images/Task%2015.7.png)

    - Move the **DevOps** account into the **Dev OU**.

   ![Task15](./Images/Task%2015.8.png)
   ![Task15](./Images/Task%2015.9.png)
   ![Task15](./Images/Task%2015.10.png)

    ![Task15](./Images/Task%2015.11.png)
2. Create a domain name for your company. I used [namecheap](www.namecheap.com). You can make use of cheap domains.
3. Create a hosted zone in AWS, and map it to your domain.Follow keenly.

![Task15](./Images/Task%2015.12.png)
![Task15](./Images/Task%2015.13.png)
![Task15](./Images/Task%2015.14.png)
![Task15](./Images/Task%2015.15.png)

## SET UP A VIRTUAL PRIVATE NETWORK (VPC)

1. Create a VPC

![Task15](./Images/Task%2015.16.png)
![Task15](./Images/Task%2015.17.png)
![Task15](./Images/Task%2015.18.png)
![Task15](./Images/Task%2015.19.png)
2. Enable the DNS hosting

![Task15](./Images/Task%2015.20.png)
![Task15](./Images/Task%2015.21.png)

3. Create internet gateway as shown in the architecture

![Task15](./Images/Task%2015.22.png)
![Task15](./Images/Task%2015.23.png)
![Task15](./Images/Task%2015.24.png)
![Task15](./Images/Task%2015.25.png)
![Task15](./Images/Task%2015.26.png)

4. Create the subnets

![Task15](./Images/Task%2015.27.png)
![Task15](./Images/Task%2015.28.png)
![Task15](./Images/Task%2015.29.png)
![Task15](./Images/Task%2015.30.png)
![Task15](./Images/Task%2015.31.png)
![Task15](./Images/Task%2015.32.png)

You can make use of this website to get the CIDR blocks easily and to understand they work.

![Task15](./Images/Task%2015.33.png)
![Task15](./Images/Task%2015.34.png)
![Task15](./Images/Task%2015.35.png)

5. Create a route tables and associate it with private and public subnets.

![Task15](./Images/Task%2015.36.png)
![Task15](./Images/Task%2015.37.png)
![Task15](./Images/Task%2015.38.png)
![Task15](./Images/Task%2015.39.png)
![Task15](./Images/Task%2015.40.png)
![Task15](./Images/Task%2015.41.png)

6. Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accessisble from the Internet).

![Task15](./Images/Task%2015.42.png)
![Task15](./Images/Task%2015.43.png)

7. Create 3 Elastic IPs

![Task15](./Images/Task%2015.44.png)
![Task15](./Images/Task%2015.45.png)
![Task15](./Images/Task%2015.46.png)
![Task15](./Images/Task%2015.47.png)

8. Create a Nat Gateway and assign one of the Elastic IPs (*The other 2 will be used by Bastion hosts)

![Task15](./Images/Task%2015.48.png)
![Task15](./Images/Task%2015.49.png)
![Task15](./Images/Task%2015.50.png)
![Task15](./Images/Task%2015.51.png)

9. Create a Security Group for:

![Task15](./Images/Task%2015.52.png)

- **Application Load Balancer:** ALB will be available from the Internet.

![Task15](./Images/Task%2015.53.png)
![Task15](./Images/Task%2015.54.png)


- **Bastion Servers:** Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address.

![Task15](./Images/Task%2015.55.png)
![Task15](./Images/Task%2015.56.png)


- **Nginx Servers:** Access to Nginx should only be allowed from a Application Load balancer (ALB).

![Task15](./Images/Task%2015.57.png)
![Task15](./Images/Task%2015.58.png)
![Task15](./Images/Task%2015.59.png)


- **Internal Application Load Balancer:** ALB will be available from the Nginx servers.

![Task15](./Images/Task%2015.60.png)


- **Webservers:** Access to Webservers should only be allowed from the Internal Application Load Balancer.

![Task15](./Images/Task%2015.61.png)

- **Data Layer:** Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) must be carefully desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have access to EFS Mountpoint.
![Task15](./Images/Task%2015.62.png)

Here are all the **security groups** created, yours should also look similar.
![Task15](./Images/Task%2015.63.png)


### TLS Certificates From Amazon Certificate Manager (ACM)
You will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB).

- Navigate to AWS ACM

![Task15](./Images/Task%2015.64.png)

- Request a public wildcard certificate for the domain name you registered.

![Task15](./Images/Task%2015.65.png)
![Task15](./Images/Task%2015.66.png)
![Task15](./Images/Task%2015.67.png)

- Use DNS to validate the domain name

![Task15](./Images/Task%2015.68.png)
![Task15](./Images/Task%2015.69.png)
![Task15](./Images/Task%2015.70.png)
![Task15](./Images/Task%2015.71.png)
![Task15](./Images/Task%2015.72.png)
![Task15](./Images/Task%2015.73.png)



## Configure EFS
- Create a new EFS File system. Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer. Associate the Security groups created earlier for data layer.

![Task15](./Images/Task%2015.74.png)
![Task15](./Images/Task%2015.75.png)
![Task15](./Images/Task%2015.76.png)
![Task15](./Images/Task%2015.77.png)
![Task15](./Images/Task%2015.78.png)
![Task15](./Images/Task%2015.79.png)
![Task15](./Images/Task%2015.80.png)
![Task15](./Images/Task%2015.81.png)

- Create 2 access points - ! for each of the website (wordpress and tooling) so that the files do not overwrite each other when we mount.

![Task15](./Images/Task%2015.82.png)
![Task15](./Images/Task%2015.83.png)
![Task15](./Images/Task%2015.84.png)
![Task15](./Images/Task%2015.85.png)
![Task15](./Images/Task%2015.86.png)
![Task15](./Images/Task%2015.87.png)


## Configure RDS
### Pre-requisite: 
- Create a KMS Key
![Task15](./Images/Task%2015.88.png)
![Task15](./Images/Task%2015.89.png)
![Task15](./Images/Task%2015.90.png)
![Task15](./Images/Task%2015.91.png)
![Task15](./Images/Task%2015.92.png)
![Task15](./Images/Task%2015.93.png)
![Task15](./Images/Task%2015.94.png)

To ensure that yout databases are highly available and also have failover support in case one availability zone fails, we will configure a multi-AZ set up of RDS MySQL database instance. In our case, since we are only using 2 AZs, we can only failover to one, but the same concept applies to 3 Availability Zones.

### To configure RDS, follow steps below:
1. Create a subnet group and add 2 private subnets (data Layer)

![Task15](./Images/Task%2015.95.png)
![Task15](./Images/Task%2015.96.png)
![Task15](./Images/Task%2015.97.png)
![Task15](./Images/Task%2015.98.png)
![Task15](./Images/Task%2015.99.png)

2. Create the DB

![Task15](./Images/Task%2015.100.png)
![Task15](./Images/Task%2015.101.png)
![Task15](./Images/Task%2015.103.png)
![Task15](./Images/Task%2015.104.png)
![Task15](./Images/Task%2015.105.png)
![Task15](./Images/Task%2015.106.png)
![Task15](./Images/Task%2015.107.png)

### Configure Loadbalancers and Target Groups

1. Create Target group for NGINX, tooling amd wordpress targets

![Task15](./Images/Task%2015.108.png)
![Task15](./Images/Task%2015.109.png)
![Task15](./Images/Task%2015.110.png)
![Task15](./Images/Task%2015.111.png)
![Task15](./Images/Task%2015.112.png)
![Task15](./Images/Task%2015.113.png)

**NOTE:** I didn't add any instance to the targets because I havent laucnhed them. I'll do that kater in and add them to the target group.

1. Create public-facing and internal loadbalancers

![Task15](./Images/Task%2015.114.png)
![Task15](./Images/Task%2015.115.png)
![Task15](./Images/Task%2015.116.png)
![Task15](./Images/Task%2015.117.png)
![Task15](./Images/Task%2015.118.png)
![Task15](./Images/Task%2015.119.png)
![Task15](./Images/Task%2015.120.png)
![Task15](./Images/Task%2015.121.png)

Repeat the same procedure for internal ALB, select wordpress as the default target

![Task15](./Images/Task%2015.122.png)

create a rule to send traffic to tooling if the headers match our specified parameters.

![Task15](./Images/Task%2015.123.png)
![Task15](./Images/Task%2015.124.png)
![Task15](./Images/Task%2015.125.png)
![Task15](./Images/Task%2015.126.png)
![Task15](./Images/Task%2015.127.png)
![Task15](./Images/Task%2015.128.png)
![Task15](./Images/Task%2015.129.png)
![Task15](./Images/Task%2015.130.png)

### Configure AMI of NGINX, Webservers(Tooling and Wordpress)

1. Launch 4 RHEL8 instances
![Task15](./Images/Task%2015.131.png)

- Install these programs on the bastion, Nginx, Wordpress and Tooling server

```
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo yum install -y wget vim python3 telnet htop git mysql net-tools chrony

sudo systemctl start chronyd

sudo systemctl enable chronyd
```

- Configure selinux policies for the webservers (Tooling, Wordpress) and nginx servers

```
sudo setsebool -P httpd_can_network_connect 1
sudo setsebool -P httpd_can_network_connect_db 1
sudo setsebool -P httpd_execmem 1
sudo setsebool -P httpd_use_nfs 1
```

- This section will instll amazon efs utils for mounting the target on the Elastic file system

```
sudo yum install -y git

git clone https://github.com/aws/efs-utils

cd efs-utils

sudo yum install -y make

sudo yum install -y go cmake rust cargo openssl-devel make rpm-build
make rpm
sudo yum install -y ./build/amazon-efs-utils*rpm
```

- Seting up self-signed certificate for the nginx instance

```
sudo mkdir -p /etc/ssl/private
sudo chmod 700 /etc/ssl/private

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/bassiy.key \
-out /etc/ssl/certs/bassiy.crt

sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```
![Task15](./Images/Task%2015.132.png)

- Seting up self-signed certificate for the apache tooling instance

```
sudo openssl req -newkey rsa:2048 -nodes \
-keyout /etc/pki/tls/private/bassiy.key \
-x509 -days 365 \
-out /etc/pki/tls/certs/bassiy.crt
sudo yum install -y httpd
sudo systemctl restart httpd
sudo systemctl enable httpd
sudo systemctl status httpd
sudo yum reinstall -y mod_ssl
sudo vim /etc/httpd/conf.d/ssl.conf
```

![Task15](./Images/Task%2015.322.png)

### Create AMIs from the instances

![Task15](./Images/Task%2015.133.png)
![Task15](./Images/Task%2015.134.png)
![Task15](./Images/Task%2015.135.png)

### Create Launch Templates

- From the created custom AMIs, create Launch templates for each of the instances

![Task15](./Images/Task%2015.136.png)
![Task15](./Images/Task%2015.137.png)
![Task15](./Images/Task%2015.138.png)
![Task15](./Images/Task%2015.139.png)
![Task15](./Images/Task%2015.140.png)
![Task15](./Images/Task%2015.141.png)

- Fill in the userdata with the details from this [repo](https://github.com/BosunEnoch7/bassiy-project-config.git) and edit it with your details

**NOTE:** For DB name, ssh into the rds engine with an instance and create a DB with a name of your choice.

![Task15](./Images/Task%2015.144.png)
![Task15](./Images/Task%2015.145.png)
![Task15](./Images/Task%2015.146.png)

Now you have your credentials
![Task15](./Images/Task%2015.142.png)

![Task15](./Images/Task%2015.143.png)


### Create AutoScaling Group

- For Bastion-server

![Task15](./Images/Task%2015.147.png)

![Task15](./Images/Task%2015.148.png)

![Task15](./Images/Task%2015.149.png)

![Task15](./Images/Task%2015.150.png)

![Task15](./Images/Task%2015.151.png)

![Task15](./Images/Task%2015.152.png)

- For Nginx-server

Insert your prefered name for Nginx-ASG

![Task15](./Images/Task%2015.153.png)

![Task15](./Images/Task%2015.154.png)

![Task15](./Images/Task%2015.155.png)

![Task15](./Images/Task%2015.156.png)

![Task15](./Images/Task%2015.157.png)

- Do same for Wordpress and Tooling, selecting the respective target groups.

![Task15](./Images/Task%2015.158.png)

### Add Records to Route 53

Add records for `tooling.bassiy.site` and `wordpress.bassiy.site`using an Alias point it to your internet facing load balancer.

![Task15](./Images/Task%2015.159.png)

![Task15](./Images/Task%2015.160.png)

![Task15](./Images/Task%2015.161.png)

![Task15](./Images/Task%2015.162.png)

![Task15](./Images/Task%2015.163.png)

- I made use of bassiy-alb as Load balacer when creating the reord instead of bassiy-internal-alb which I had already created added rule to forward to tooling target group, so I will need to recreate target groups for tooling and wordpress webpage.
Note that this is a mistake from my end. Though I would have decided to recreate the records but I just wish to try it out this way and learn a new thing.

![Task15](./Images/Task%2015.164.png)
![Task15](./Images/Task%2015.165.png)
![Task15](./Images/Task%2015.166.png)
![Task15](./Images/Task%2015.167.png)

Now, lets add rule to bassiy-alb

![Task15](./Images/Task%2015.168.png)
![Task15](./Images/Task%2015.169.png)
![Task15](./Images/Task%2015.170.png)

### Test your webpages

![Task15](./Images/Task%2015.171.png)

If you ecounter this same issue, let's troubleshoot/fix. follow below images keenly.

![Task15](./Images/Task%2015.173.png)

- Add HTTP with ALB security Group as source to tooling server SG(bassiy-webservers-sg)

![Task15](./Images/Task%2015.174.png)

- SSH and check if Apache is active (running) on tooling server

![Task15](./Images/Task%2015.175.png)

- Closely run the following image commands

![Task15](./Images/Task%2015.176.png)

![Task15](./Images/Task%2015.177.png)

- Clone your tooling [repo](https://github.com/BosunEnoch7/tooling.git)

![Task15](./Images/Task%2015.178.png)

- Then run the blow commads

![Task15](./Images/Task%2015.179.png)

- Now that we see HTTP/1.1 200 OK, let's reload tooling webpage.

```
<your-domain-name>.bassey.site
```
![Task15](./Images/Task%2015.180.png)

```
<your-domain-name>.bassiy.site/login.php
```
![Task15](./Images/Task%2015.181.png)