# LAMP STACK STACK IMPLEMENTATION IN AWS (Linux, Apache, MySQL, PHP) 

**This document details the provisioning of a LAMP stack (Linux, Apache, MySQL, and PHP) on an AWS EC2 instance. The setup process was executed as part of environment configuration, and the screenshots below serve as deployment artifacts, presented in the order they were captured during the stack initialization.**

# Step 0 – Preparing prerequisites
 In order to complete this project, you will need an AWS account and a virtual server with Ubuntu 
Server OS. 

● Register an AWS accout if you have none

● Launch a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM) on AWS.

● Connect to the instance on your local machine.
● You will have an outcome as this after a successful connection to your local machine

# STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL 

Install Apache using Ubuntu’s package manager ‘apt’

● Update a list of packages in package manager by running `sudo apt update`


● Run apache2 package installation `sudo apt install apache2`

● To verify that apache2 is running as a Service in our OS, use following command `sudo systemctl status apache2`

If it is green and running, then you did everything correctly – you have just launched your first 
Web Server in the Clouds! 

● Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is the 
default port that web browsers use to access web pages on the Internet 
As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so 
we need to add a rule to EC2 configuration to open inbound connection through port 80: 

● First, let us try to check how we can access it locally in our Ubuntu shell, run: `curl http://localhost:80 ` or `curl http://<your-ip-address>`

● Now it is time for us to test how our Apache HTTP server can respond to requests from the 
Internet. 
Open a web browser of your choice and try to access following url http://Public-IP-Address:80 to view it

Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to 
use following command:`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

If you see following page, then your web server is now correctly installed and accessible through 
your firewall. 

In fact, it is the same content that you previously got by ‘curl’ command, but represented in 
nice HTML formatting by your web browser.

# STEP 2 — INSTALLING MYSQL 

● Again, use `apt` to acquire and install this software:`sudo apt install mysql-server`

● When prompted, confirm installation by typing Y, and then ENTER.


●  When the installation is finished, log in to the MySQL console by typing: `sudo mysql`

● This will connect to the MySQL server as the administrative database user root, which is 
inferred by the use of sudo when running this command. You should see output like this: 

● It’s recommended that you run a security script that comes pre-installed with MySQL. This script 
will remove some insecure default settings and lock down access to your database system. 
Before running the script, you will set a password for the root user, using 
mysql_native_password as default authentication method. We’re defining this user’s password 
as PassWord.1. `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

● Exit the MySQL shell with: `mysql> exit`

● Start the interactive script by running: `$ sudo mysql_secure_installation `

● This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

● Answer Y for yes, or anything else to continue without enabling. 

● If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if 
you enter 2 for the strongest level, you will receive errors when attempting to set any password 
which does not contain numbers, upper and lowercase letters, and special characters, or which is 
based on common dictionary words e.g., PassWord.1. 

● If you enabled password validation, you’ll be shown the password strength for the root password 
you just entered and your server will ask if you want to continue with that password. If you are 
happy with your current password, enter Y for “yes” at the prompt: 

● Estimated strength of the password: 100  
Do you wish to continue with the password provided? (Press y|Y for Yes, any other key for No): 
y

● For the rest of the questions, press Y and hit the ENTER key at each prompt. This will prompt 
you to change the root password, remove some anonymous users and the test database, disable 
remote root logins, and load these new rules so that MySQL immediately respects the changes 
you have made.

● When you’re finished, test if you’re able to log in to the MySQL console by typing: `sudo mysql -p`

Notice the -p flag in this command, which will prompt you for the password used after changing 
the root user password. 

● To exit the MySQL console, type: `mysql> exit `

Notice that you need to provide a password to connect as the root user. For increased security, it’s best to have dedicated user accounts with less expansive privileges set 
up for every database, especially if you plan on having multiple databases hosted on your server. 



# STEP 3 — INSTALLING PHP 

You have Apache installed to serve your content and MySQL installed to store and manage your 
data. https://www.php.net/ is the component of our setup that will process code to display dynamic content to the 
end user. In addition to the **php** package, you’ll need **php-mysql**, a PHP module that allows PHP 
to communicate with MySQL-based databases. You’ll also need **libapache2-mod-php** to enable 
Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies. 

● To install these 3 packages at once, run: `sudo apt install php libapache2-mod-php php-mysql `

● Once the installation is finished, you can run the following command to confirm your PHP 
version: `php -v`



At this point, your LAMP stack is completely installed and fully operational. 

● Linux (Ubuntu)

● Apache HTTP Server
 
● MySQL 

● PHP 


# STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE 

Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve 
documents from the **/var/www/html** directory. We will leave this configuration as is and will add our own directory next next to the default one. 

● Create the directory for projectlamp using **mkdir** command as follows: `sudo mkdir /var/www/projectlamp`

● Next, assign ownership of the directory with your current system user: `sudo chown -R $USER:$USER /var/www/projectlamp`

● Then, create and open a new configuration file in Apache’s sites-available directory using your 
preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way): `sudo vi /etc/apache2/sites-available/projectlamp.conf`

● This will create a new blank file. Paste in the following bare-bones configuration by hitting 
on i on the keyboard to enter the insert mode, and paste the text: `<VirtualHost *:80> 
ServerName projectlamp 
ServerAlias www.projectlamp  
ServerAdmin webmaster@localhost 
DocumentRoot /var/www/projectlamp 
ErrorLog ${APACHE_LOG_DIR}/error.log 
CustomLog ${APACHE_LOG_DIR}/access.log combined 
</VirtualHost>`

To save and close the file, simply follow the steps below: 
1. Hit the`esc`button on the keyboard 
2. Type: 
3. Type`wq.`w for write and q for quit 
4. Hit`ENTER`to save the file

