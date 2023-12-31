# My REST API
In this project I have created my own REST API to access to a collection of articles from a locally hosted database.

- I have used all the HTTP Request Verbs (GET, POST, PUT, PATCH and DELETE)
- I have implemented the specific patterns of routes/ endpoint URLs in accordance with the standard guidelines of RESTful Routing.    

![RESTful Routing guidelines](https://i.ibb.co/yR2XTLs/RESTful-Routing.png)
    
- There is no front end interface of this project

## Steps I have followed
1. Used `mongod` command from a terminal to start a local database server from localhost:27017
2. Used `mongosh` command from another terminal to access to the local database with MongoDB shell.
3. Created a sample database named WikiDB, using the command `use WikiDB`
4. Created a collection named 'articles' with 4 sample documents, using the command `db.articles.insertMany({})`
5. Created a dedicated directory for the project and inside that directory initialised NPM using `npm init -y` command from another terminal.
6. Used `npm install express body-parser ejs mongoose` command to install all the required dependencies for the project. 
7. Started the server with the following boiler plate codebase:
```javascript
// Requiring necessary NPM modules:
const express = require("express");
const bodyParser = require("body-parser");
const ejs = require("ejs");
const mongoose = require('mongoose');

// Set port for deployement as well as localhost:
const port = process.env.PORT || 3000;

// Initial Setup for the App:
const app = express();
app.set('view engine', 'ejs');
app.use(bodyParser.urlencoded({extended: true}));
app.use(express.static("public"));

// Connect to a new MongoDB Database, using Mongoose ODM:

// Create a new collection to store the items:


// Handle HTTP Requests


// Enable client to listen to the appropriate port:
app.listen(port, () => {
  console.log(`Server started on port ${port}`);
});
```          
                 
8. Modified the server code to connect to our WikiDB database and the articles collection inside it:
```javascript
// Connect to a new MongoDB Database, using Mongoose ODM:
mongoose.connect('mongodb://localhost:27017/wikiDB');

// Create a new collection to store the articles:
const articleSchema = new mongoose.Schema ({
	title: {
    type: String,
    required: true
  },
	content: String
})
const Article = mongoose.model('Article', articleSchema);
```    

9. Handled HTTP `GET`, `POST` and `DELETE` requests made on the `/articles` route:
```javascript
app.route('/articles')
	.get( (req, res) => {
    Article.find({}, (err, articles) => {
  		if(!err) {
  			res.send(articles);
  		} else {
        res.send(err);
      }
  	})
	})
	.post( (req, res) => {
    const title = req.body.title;
    const content = req.body.content;
    const article = new Article({title, content});
    article.save(err => {
      if(!err) {
        res.send("Successfully added a new article.");
      } else {
        res.send(err);
      }
    })
	})
	.delete( (req, res) => {
    Article.deleteMany({}, err => {
      if(err) {
        res.send(err);
      } else {
        res.send("Successfully deleted all articles");
      }
    })
	});
```    

10. Handled HTTP `GET`, `PUT`, `PATCH` and `DELETE` requests made on the `/articles/:customURL` route:
```javascript
app.route('/articles/:customURL')
  .get( (req,res) => {
    Article.findOne({ title: req.params.customURL }, (err, article) => {
      if(!err) {
        if(article) {
    			res.send(article);
    		} else {
          res.send("No record found");
        }
      } else {
        res.send(err);
      }
  	})
  })
  .put((req, res) => {
    Article.replaceOne(
      {title: req.params.customURL},
      {title: req.body.title, content: req.body.content},
      (err) => {
        if(!err) {
          res.send("Successfully replaced the selected article.")
        } else {
          res.send(err);
        }
      }
    )
  })
  .patch((req, res) => {
    Article.updateOne(
      {title: req.params.customURL},
      req.body,
      (err, result) => {
        if(!err) {
          res.send("Successfully updated the selected article.")
        } else {
          res.send(err);
        }
      }
    )
  })
  .delete( (req, res) => {
    Article.deleteOne({title: req.params.customURL}, err => {
      if(err) {
        res.send(err);
      } else {
        res.send("Successfully deleted the selected article");
      }
    })
	});
```

