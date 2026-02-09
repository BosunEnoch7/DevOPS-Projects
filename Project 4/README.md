# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS
Now, that you have already learned how to deploy **LAMP**, **LEMP** and **MERN** Web stacks – it is time to get yourself familiar with the MEAN stack and deploy it to the Ubuntu server.

**MEAN Stack is a combination of the following components:**
1.	**MongoDB** (Document database) – Stores and allows retrieval of data.
2.	**Express** (Back-end application framework) – Makes requests to Database for Reads and Writes.
3.	**Angular** (Front-end application framework) – Handles Client and Server Requests
4.	**Node.js** (JavaScript runtime environment) – Accepts requests and displays results to end user

## Step 0 – Preparing prerequisites 

In order to complete this project you will need an AWS account and a virtual server with **Ubuntu Server OS.**
If you do not have an **AWS** account – go back to Project 1 Step 0 to sign in to AWS free tier account and create a new EC2 Instance of t2.nano family with an Ubuntu Server 20.04 LTS (HVM) image. Remember, you can have multiple EC2 instances, but make sure you STOP the ones you are not working with at the moment to save available free hours.

## Step 1: Install NodeJs

**Node.js** is a JavaScript runtime built on Chrome’s V8 JavaScript engine. **Node.js** is used in this tutorial to set up the Express routes and AngularJS controllers.
Update Ubuntu

`sudo apt update`
![Task4](./Images/Task%204.1.png)
Upgrade ubuntu

`sudo apt upgrade`

![Task4](./Images/Task%204.2.png)

Add certificates

```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
```
```
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```
![Task4](./Images/Task%204.3.png)

**Install NodeJS**

```sudo apt install -y nodejs```
![Task4](./Images/Task%204.4.png)

Check node and npm version
```
node -v
npm -v
```
![Task4](./Images/Task%204.5.png)
# Step 2: Install MongoDB

MongoDB stores data in flexible, **JSON-like** documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.
mages/WebConsole.gif
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

```
![Task4](./Images/Task%204.6.png)

Update Apt

`sudo apt update`

`sudo apt install -y curl gnupg ca-certificates`

![Task4](./Images/Task%204.7.png)

**Install MongoDB**

`sudo apt install -y mongodb-org`

![Task4](./Images/Task%204.9.png)

Start & Enable MongoDB:

```
sudo systemctl start mongod

sudo systemctl enable mongod
Start The server

```

Check Status If Actively Running:

`sudo systemctl status mongod`

![Task4](./Images/Task%204.10.png)

Test It To Open and then exit :

`mongosh`
![Task4](./Images/Task%204.11.png)

Install npm – Node package manager:

`sudo apt install -y npm`


Install [body-parser](https://www.npmjs.com/package/body-parserpackage),
We need ‘body-parser’ package to help us process **JSON files** passed in requests to the server.

`sudo npm install body-parser`
![Task4](./Images/Task%204.12.png)

Create a folder named ‘Books’

`mkdir Books && cd Books`

In the Books directory, Initialize npm project

`npm init`
 ![Task4](./Images/Task%204.13.png)

Add a file to it named **server.js**

`vi server.js`

Copy and paste the web server code below into the **server.js** file.

```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```
![Task4](./Images/Task%204.14.png)

## INSTALL EXPRESS AND SET UP ROUTES TO THE SERVER

**Step 3: Install [Express](https://expressjs.com/) and set up routes to the server**

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express to pass book information to and from our MongoDB database.

We also will use [Mongoose](https://mongoosejs.com/) package which provides a straightforward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.

`sudo npm install express mongoose`

![Task4](./Images/Task%204.15.png)

In ‘Books’ folder, create a folder named **apps**

`mkdir apps && cd apps`

Create a file named **routes.js**

`vi routes.js`
 
Copy and paste the code below into routes.js

```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```
![Task4](./Images/Task%204.16.png)

In the ‘apps’ folder, create a folder named **models**

````mkdir models && cd models````

Create a file named **book.js**

`vi book.js`

Copy and paste the code below into ‘book.js’

```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```
![Task4](./Images/Task%204.17.png)
## Step 4 – Access the routes with [AngularJS](https://angularjs.org/)

**AngularJS** provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.
Change the directory back to ‘Books’

``cd ../..``

Create a folder named **public**

``mkdir public && cd public``

Add a file named **script.js**

``vi script.js``

 
Copy and paste the Code below (controller configuration defined) into the script.js file.
```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```
![Task4](./Images/Task%204.18.png)

In the **public** folder, create a file named **index.html**;

``vi index.html``
 
 
 
 
Copy and paste the code below into **index.html** file.
```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>
 
        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>
 
          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```
![Task4](./Images/Task%204.19.png)

Change the directory back up to **Books**

``cd ..``

Start the server by running this command:

``node server.js``

This is the blocker I experienced at my end;
![Task4](./Images/Task%204.19a.png)

This shows that Node v24.13.0 is being run with Express 5. Express 5 changed how it reads route paths, making the use of the `*` wildcard on line 22 of ``server.js`` illegal.

How to resolve this if you're also experiencing such; 


- Express 4 is the industry standard that still supports the `*` syntax that is currently in your code.

- By running ``npm install express@4``, this will bypass **PathError** without having to manually rewrite your JavaScript code.

- To avoid permission denied, change ownership with this command;

`chown -R $USER:$USER .`

- and then run 

``npm install express@4``

``npm install``

![Task4](./Images/Task%204.19b.png)

- then run ``node server.js`` again.

![Task4](./Images/Task%204.20.png)

The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what the curl command returns locally.

``curl -s http://localhost:3300``

For this – you need to open TCP port **3300** in your AWS Web Console for your EC2 Instance.
You are supposed to know how to do it, if you have forgotten – refer to **Project 1** (Step 1 — Installing Apache and Updating the Firewall)
Your security group shall look like this:

![Task4](./Images/Task%204.21.png)

Now you can access our Book Register web application from the Internet with a browser using a Public IP address or Public DNS name.

This is how your WebBook Register Application will look in the browser:
![Task4](./Images/Task%204.22.png)
