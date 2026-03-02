# IMPLEMENTATION OF A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS).

## CLIENT-SERVER ARCHITECTURE WITH MYSQL

- Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another. In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server". A simple diagram of Web Client-Server architecture is presented below:

![Task5](./Images/Task%205.17.png) 

- In the example above, a machine that is trying to access a Web site using a Web browser or simply ‘curl’ command is a client and it sends HTTP requests to a Web server (Apache, Nginx, IIS or any other) over the Internet. If we extend this concept further and add a Database Server to our architecture, we can get this picture:

![alt](./Images/Task%205.18.png)

- The Web Server has a role of a "Client" that connects and reads/writes to/from a Database (DB) Server (MySQL, MongoDB, Oracle, SQL Server or any other), and the communication between them happens over a Local Network (it can also be an Internet connection, but it is a common practice to place Web Server and DB Server close to each other in a local network). Essentially, it is sending requests to the remote server, and in turn, would be expecting some kind of response from the remote server.

## TO DEMONSTRATE A BASIC CLIENT-SERVER USING MYSQL RELATIONAL DATABASE MANAGEMENT SYSTEM (RDBMS), FOLLOW THE BELOW INSTRUCTIONS:

1. Create and configure two Linux-based virtual servers (EC2 instances in AWS).

```Server A name - mysql-server``` and  ```Server B name - mysql-client```

![alt](./Images/Task%205.1.png)

- On mysql-server Linux Server install MySQL Server software.

`sudo apt update`

- Then install the mysql-server package:  
`sudo apt install mysql-server`

![alt](./Images/Task%205.2.png) 
- Ensure that the server is running using the systemctl command:  
`sudo systemctl start mysql.service`

`sudo systemctl status mysql.service`

![alt](./Images/Task%205.3.png) 

## SETTING IT UP

`sudo mysql`

For the password I'll be using 'PassWord.1'  
```ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';```
![a](./Images/Task%205.4.png)

Run a MySQL secure installation  
`sudo mysql_secure_installation`

![a](./Images/Task%205.5.png) 

Answer Y for yes, or anything else to continue without enabling.

- In the MySQL server create a user and a database named first_db and a user named first_user, but you can replace these names with different values.

- First, connect to the MySQL console using the root account:  
`sudo mysql -p`

![a](./Images/Task%205.6.png)
- Create a new database by running this command from your MySQL console:  
`CREATE DATABASE example_database;`

- Create a new user and grant full privileges on the database we have just created.  
`CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

![a](./Images/Task%205.7.png)

***Note: The following command above creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.***

- Give this user permission over the example_database database:  
`GRANT ALL ON example_database.* TO 'example_user'@'%';`

![a](./Images/Task%205.8.png)

***Note: This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.***

Exit the MySQL shell with: `exit`

- Test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:  
`mysql -u example_user -p`

![Task5](./Images/Task%205.9.png)

***The -p flag in this command, which will prompt us for the password used when creating the example_user user.***

- After logging in to the MySQL console, confirm that you have access to the example_database database: mysql> `SHOW DATABASES;`

This will give you the following output:

![Task5](./Images/Task%205.10.png)
- Exit MySQL and restart the mySQL service using  
- `sudo systemctl restart mysql`        `sudo systemctl status mysql.service`

![Task5](./Images/Task%205.11.png)

- You might need to configure MySQL server to allow connections from remote hosts.  
- `sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf`

![Task5](./Images/Task%205.12.png)

- By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups.

![Task5](./Images/Task%205.13.png)
***Mysql client private ip address is used above instead of 0.0.0.0 for extra Security***

- Save the above configurations.

## SET UP MYSQL CLIENT
- ssh into mysql-client instance

- On mysql client Linux Server install MySQL client software.  
`sudo apt update && sudo apt ugrade`

![Task5](./Images/Task%205.14.png)

- install the mysql-client package:
`sudo apt install mysql-client -y`

![Task5](./Images/Task%205.15.png)

- From mysql client instance connect remotely to mysql server Database using:  
`sudo mysql -u example_user -h <mysqlserver private ip> -p`

- Type this in and the database should be visible:
`show databases;`

![Task5](./Images/Task%205.16.png)