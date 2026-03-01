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

```
Start The server

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
const express = require('express');
const router = express.Router();

const Book = require('./models/book'); // <-- correct path (apps/models/book.js)

// GET all books
router.get('/api/books', async (req, res) => {
  try {
    const books = await Book.find({}).sort({ _id: -1 });
    return res.json(books);
  } catch (err) {
    return res.status(500).json({ error: err.message });
  }
});

// POST add a book
router.post('/api/books', async (req, res) => {
  try {
    const { name, isbn, author, pages } = req.body;

    if (!name || !isbn || !author || pages === undefined) {
      return res.status(400).json({ error: 'name, isbn, author, pages are required' });
    }

    // prevent duplicate ISBN
    const exists = await Book.findOne({ isbn: String(isbn).trim() });
    if (exists) {
      return res.status(409).json({ error: 'A book with this ISBN already exists' });
    }

    const book = await Book.create({
      name: String(name).trim(),
      isbn: String(isbn).trim(),
      author: String(author).trim(),
      pages: Number(pages),
    });

    return res.status(201).json({ message: 'Successfully added book', book });
  } catch (err) {
    return res.status(500).json({ error: err.message });
  }
});

// DELETE by isbn
router.delete('/api/books/:isbn', async (req, res) => {
  try {
    const isbn = String(req.params.isbn).trim();
    const deleted = await Book.findOneAndDelete({ isbn });

    if (!deleted) {
      return res.status(404).json({ error: 'Book not found' });
    }

    return res.json({ message: 'Successfully deleted the book', book: deleted });
  } catch (err) {
    return res.status(500).json({ error: err.message });
  }
});

module.exports = router;
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
const form = document.getElementById('bookForm');
const msg = document.getElementById('msg');
const tbody = document.getElementById('tbody');
const addBtn = document.getElementById('addBtn');
const search = document.getElementById('search');

let allBooks = [];

function setMsg(text, type) {
  msg.textContent = text || '';
  msg.className = 'msg ' + (type || '');
}

function escapeHtml(s) {
  return String(s).replace(/[&<>"']/g, (m) => ({
    '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;'
  }[m]));
}

function render(books) {
  if (!books.length) {
    tbody.innerHTML = `<tr><td colspan="5">No books yet.</td></tr>`;
    return;
  }

  tbody.innerHTML = books.map(b => `
    <tr>
      <td>${escapeHtml(b.name ?? '')}</td>
      <td>${escapeHtml(b.isbn ?? '')}</td>
      <td>${escapeHtml(b.author ?? '')}</td>
      <td>${escapeHtml(b.pages ?? '')}</td>
      <td class="actions">
        <button data-isbn="${escapeHtml(b.isbn)}">Delete</button>
      </td>
    </tr>
  `).join('');
}

async function loadBooks() {
  try {
    const res = await fetch('/api/books');
    const data = await res.json();
    allBooks = Array.isArray(data) ? data : [];
    applySearch();
  } catch (e) {
    tbody.innerHTML = `<tr><td colspan="5">Failed to load books.</td></tr>`;
  }
}

function applySearch() {
  const q = (search.value || '').toLowerCase().trim();
  if (!q) return render(allBooks);

  const filtered = allBooks.filter(b =>
    String(b.name || '').toLowerCase().includes(q) ||
    String(b.author || '').toLowerCase().includes(q) ||
    String(b.isbn || '').toLowerCase().includes(q)
  );
  render(filtered);
}

form.addEventListener('submit', async (e) => {
  e.preventDefault();
  setMsg('');

  const name = document.getElementById('name').value.trim();
  const isbn = document.getElementById('isbn').value.trim();
  const author = document.getElementById('author').value.trim();
  const pages = Number(document.getElementById('pages').value);

  if (!name || !isbn || !author || !pages) {
    setMsg('Please fill all fields correctly.', 'err');
    return;
  }

  addBtn.disabled = true;
  addBtn.textContent = 'Adding...';

  try {
    const res = await fetch('/api/books', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name, isbn, author, pages })
    });

    const data = await res.json();

    if (!res.ok) {
      setMsg(data.error || 'Failed to add book.', 'err');
    } else {
      setMsg('Book added successfully ✅', 'ok');
      form.reset();
      await loadBooks();
    }
  } catch (err) {
    setMsg('Network error while adding book.', 'err');
  } finally {
    addBtn.disabled = false;
    addBtn.textContent = 'Add Book';
  }
});

tbody.addEventListener('click', async (e) => {
  const btn = e.target.closest('button[data-isbn]');
  if (!btn) return;

  const isbn = btn.getAttribute('data-isbn');
  if (!confirm(`Delete book with ISBN ${isbn}?`)) return;

  try {
    const res = await fetch(`/api/books/${encodeURIComponent(isbn)}`, {
      method: 'DELETE'
    });
    const data = await res.json();
    if (!res.ok) {
      setMsg(data.error || 'Failed to delete book.', 'err');
    } else {
      setMsg('Book deleted ✅', 'ok');
      await loadBooks();
    }
  } catch (err) {
    setMsg('Network error while deleting.', 'err');
  }
});

search.addEventListener('input', applySearch);

// initial load
loadBooks();
```
![Task4](./Images/Task%204.18.png) 

In the **public** folder, create a file named **index.html**;

``vi index.html``
 
 
 
 
Copy and paste the code below into **index.html** file.
```
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Books</title>
  <style>
    body { font-family: Arial, sans-serif; background:#f6f7fb; margin:0; }
    .wrap { max-width: 900px; margin: 40px auto; padding: 0 16px; }
    .card { background:#fff; border-radius:12px; padding:16px; box-shadow:0 6px 18px rgba(0,0,0,.08); }
    h1 { margin: 0 0 14px; font-size: 50px; }
    .grid { display:grid; grid-template-columns: repeat(4, 1fr); gap:10px; }
    .grid label { font-size: 12px; color:#333; display:block; margin-bottom:6px; }
    input { width:100%; padding:10px; border:1px solid #d7dbe7; border-radius:10px; outline:none; }
    input:focus { border-color:#6b7cff; }
    .row { display:flex; gap:10px; align-items:flex-end; margin-top: 12px; }
    button { padding:10px 14px; border:none; border-radius:10px; background:#2d5bff; color:#fff; cursor:pointer; }
    button:disabled { opacity:.6; cursor:not-allowed; }
    .msg { margin-top: 10px; font-size: 13px; }
    .msg.ok { color: #0a7a2f; }
    .msg.err { color: #b00020; }

    table { width:100%; border-collapse: collapse; margin-top: 14px; }
    th, td { text-align:left; padding:10px; border-bottom: 1px solid #eef0f6; font-size: 14px; }
    th { font-size: 12px; text-transform: uppercase; letter-spacing: .04em; color:#556; }
    .actions button { background:#ff3b30; }
    .topbar { display:flex; justify-content:space-between; align-items:center; gap:10px; }
    .search { max-width: 260px; }
    @media (max-width: 760px){
      .grid { grid-template-columns: 1fr 1fr; }
      .topbar { flex-direction: column; align-items: stretch; }
      .search { max-width: 100%; }
    }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <div class="topbar">
        <h1>📚 Book Manager</h1>
        <input class="search" id="search" placeholder="Search by name / author / isbn..." />
      </div>

      <form id="bookForm">
        <div class="grid" style="margin-top:12px;">
          <div>
            <label for="name">Name</label>
            <input id="name" required />
          </div>
          <div>
            <label for="isbn">ISBN</label>
            <input id="isbn" required />
          </div>
          <div>
            <label for="author">Author</label>
            <input id="author" required />
          </div>
          <div>
            <label for="pages">Pages</label>
            <input id="pages" type="number" min="1" required />
          </div>
        </div>
        <div class="row">
          <button id="addBtn" type="submit">Add Book</button>
          <div id="msg" class="msg"></div>
        </div>
      </form>

      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>ISBN</th>
            <th>Author</th>
            <th>Pages</th>
            <th></th>
          </tr>
        </thead>
        <tbody id="tbody">
          <tr><td colspan="5">Loading...</td></tr>
        </tbody>
      </table>
    </div>
  </div>

  <script src="script.js"></script>
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
![Task4](./Images/Task%204.23.png)

Adding Books
![Task4](./Images/Task%204.24.png)

Deleting Books
![Task4](./Images/Task%204.25.png) 