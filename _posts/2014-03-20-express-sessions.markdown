---
layout: default
title:  "Express Sessions"
date:   2014-03-20 17:50:00
categories: main
tags: ['NodeJS', 'JS', 'ExpressJS', 'Sessions']
---

# Express Sessions
***

### What are sessions ?
Let me explain this in plain words than the wiki type of definition. Let us take the client server communication as it is our current requirement, the awareness or identification of the client in the server every time the client calls the server with out necessarily signing in every time. 
So when we elaborate the procedure, we have the following steps as that is how we are going to implement.

* The client sending a signin request with its credentials
* Server checks the credentials and if they are verified it will create a session, the session can be an in-memroy item or a db entry. We create a globally unique identifier for this entry
* Send the SessionID created above to the client as a cookie.
* Every new request from the client comes with the same cookie, so from the cookie we get the session Id and use to get the session record which is either in the db or in-memory.

This is how sessions will be used in a web application. Lets see how we can use it in the Express framework.

Lets follow the same steps while using Express framework. I am persisting the sessions in MongoDB you can look into other post explaing the MongoDB usage in NodeJS.

You can always check this [wiki definition][1] to add more details.


### Express Session middleware

Session middleware provided by Express reads the user cookies and populates the req object with sessions to use `req.session` inside the controller. 

Every time a user requests the web page from a browser Express will try to indentify the client, if it cannot then there will be a new session created in the server which gets used till the browser is closed and re-opened. But when the user logs in you can set the session information into the cookies so as long as the the cookie is available in the browser(Which usually do not get deleted) the middleware can fetch the session record and populate the `req.session`.

Enabling Express to persist and retreive the sessions from cookie via connecting to the MongoDB.
	
	// Its better to have the db url in a config
	var dbUrl = "mongodb://localhost/express";
	
	// session options. Looks for 'sid' in the cookie keys
	// default value is 'connect.sid'
	var options = {
		secret: 'Express is awesome',
		key: 'sid',
		store: new mongoStore({
			url: dbUrl,
			collection: 'sessions'
		}),
		cookie: {
			path: '/',
			httpOnly: true,
			expires: (1000 * 60 * 60 * 24 * 30 * 6) //180 Days
		}
	}
		
	// using as a middle
	app.use(express.session(options));
 
If we can elaborate this, `express.session` is the middleware which will take the options containing 

* DB configurations to persist the sessions
* Cookie key name to look for the sessionId and a 'secret' to validate the session key
* Cookie attributes and expiry durations

Note: A small note on the cookie configuration. The path set to '/' will send the cookie to all the routes in the site with the same host not for the sub-domain though. Expiry info says that the cookie is valid for that long which is to be managed by the server.

So lets say that the client made a login request to the server and the login controller starts checking for the  credentials and if the credentials are verified save a cookie and respond to the client. Let us see the code snippet in the following steps

Import mongoose which is the ORM for MongoDB, Get a handle to mongoose model. Also register the express route for login request



	// ORM for mongoDB
	var mongoose = require('mongoose');
	
	// Getting the mongoose model
	var User = mongoose.model('User');
	
	// Route for post request to '/login'
	app.post('/login', login);

Login controller retreives the post parameters
	

	// Fetch the post parameters from the request
	var email = req.param('email');
	var password = req.param('password');

Find the user with the given emailID and verify his password 

	User.findOne({email:email}, function(err, obj){
		if (obj.password == password){

			// Am just showing that more info can be saved in the session
			req.session.user = { email : email };
	
			res.send({code : 1, message : 'Successful'});
		}
		else
			res.send({code: 0, message: 'invalid password'});
	});
	
Joining the pieces to a complete code. Also added few error checks if finding users throws an error.
	
    // controller for user login request
    var login = function(req, res, next){

      // Fetch the post parameters from the request
      var email = req.param('email');
      var password = req.param('password');
      
      //finding single user record with email equal to email user given
      User.findOne({email:email}, function(err, obj){
       if(err){
         next(err);
       }
       else if (!obj){
         next(new Error('User Not found'));
       }
       else {
         // if the user record's password is equal to provided
         // password, then we can log him in.
         if (obj.password == password){
      
           // Am just showing that more info can be saved in the session
           req.session.user = {
             email : email
           };
      
           //Adding a cookie to the 'res' object
           res.cookie('sid', res.req.sessionID)
           res.send({code : 1, message : 'Successful'});
         }
         else
           res.send({code: 0, message: 'invalid password'});
       }
      });
    };
	
Once the you are sure the login request is successful, you can make a new call to a different route and try to log the cookies.

	// Route for checking the dashboard
	app.get('/dashboard', dashboard);
	
	var dashboard = function(req, res, next){
		//Let us display it in the web page temporarily
		res.send({
			session : req.session,
			cookies : req.cookies
		});
	} 
	
Now try opening this in the browser [http://localhost:3000/dashboard][2] to see an output like

	{
	   "session": {
		  "cookie": {
			"originalMaxAge": 15552000000,
			"expires": "2014-09-23T15:52:41.531Z",
			"httpOnly": true,
			"path": "/"
		  },
		  "user": {
			"email": "prakash@noplug.in"
		  }
	  },
	  "cookies": {
		"sid": "s:j3tJKj7MEQ42uzCluzyoBqJl.NTfXcE/Fvrb+i33cd8d9UzLMbLa6kTlGXZ/TTGwr4ns"
	  }
	}

Check [Role based responses][3] to know more about routing and customizing them.

[1]: http://en.wikipedia.org/wiki/Session_(computer_science)
[2]: http://localhost:3000/dashboard
[3]: http://www.noplug.in/blogs/role-based-response