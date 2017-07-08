---
layout: default
title:  "Role based access"
date:   2014-04-26 17:40:00
categories: main
tags: ['NodeJS', 'Roles']
--- 

# Role based access
***

This is a usual requirement in any web application. You would want your web application to handle different authorization levels like an administrator, power user, public user etc., 

We will see how to implement the role based access with NodeJS Express framework.

Have a look at these articles for more clarity on the concepts I am talking about

* [configuring Express][1]
* [using Express Sessions][2] and 
* [using mongodb][3]

Lot of the following steps might be found in the above express sessions article but the main change is the role of the user and using it in the route handlers. And this is an important concept so we shall go forward even though there are some redundancies.

We have few following steps to see the usage of role based access with the help of few of Express libraries

1. Adding a role to model 
2. A login controller
3. Saving some of user information in the session
4. middleware for checking the roles

#### Creating a User model with role
Lets accomodate our user schema with a new field called `role`

	var mongoose = require('mongoose');
	var Schema = mongoose.Schema;
	
	var UserSchema = new Schema({
		name: String,
		username : String,
		
		// we will see in later articles how to use salt and saving an
		// encrypted password than a plain one in the database.
		password : String,

		//admin, user
		role : String 
	});
	
	mongoose.model('User', UserSchema);

We will now need a form for signup and signin to call the server. I will cover the content on creating forms in the article '[using JADE][4]'. So design your forms from that article or you always download the [code][5]

#### Session Support

Enable the session support provided by the Express. You can read about the express sessions from my other [post][2]. For simplicity I am adding the code regarding the sessions below. Express will create a session and save as cookie in client for every user who accesses the web application if the client doesnot send the cookie along as server cannot identify the client (the cookie in the client might be deleted if it is an incognito or a private browsing)

Express session middleware 

	var dbUrl = 'mongodb://localhost/express';
	
	//Sessions to be stored in the MongoDB
	var sessionStore = new mongoStore({
			url: dbUrl,
			collection: 'sessions'
		});
	
	var options = {
		secret: 'express is awesome',
		key: 'sid',
		store: sessionStore,
		cookie:{
			path: '/',
			httpOnly: true,
			expires: (1000 * 60 * 60 * 24 * 30 * 6) //180 Days
		}	
	};
	
	app.use(express.session(options));


#### Login Controller

I am going to create 2 routes apart from what u get in the basic express project scaffold which will handle get and post for login and register. You can create the routes for registering a user, right now am assuming that there are 2 users in my database.

	//Admin User
	{
		name: 'Admin',
		password: 'admin123',
		username: 'admin',
		role: 'admin'
	}
	
	//A normal logged in user
	{
		name: 'Prakash',
		password: 'prakash123',
		username: 'prakash',
		role: 'user'
	}

The login routes

	//A get route for getting the earlier loginPage jade
	app.get('/login', loginPage);
	
	//A post route on making a login
	app.post('/login', login);

Let us start creating Controllers for the login routes

	
	var loginPage = function(req, res){
		res.render('user/login');
	};
	
	
	var login = function(req, res, next){
		var username = req.param('username');
		var password = req.param('password');	

		//	Find with the model for a user who has this username and verify his password
		User.findOne({username: username}, function(err, obj){
			if(err){
				//Forwarding to next error middle of express
				next(err); 
			}
			else if (!obj){
				//Forwarding to next error middle of express
				next(new Error('User Not found'));
			}
			else {
			
			     if(obj.password == password){
			     	// This guy is authenticated so we shall save his role into the session
			     	// along with user record's unique id
				     req.session.user = {
					     role : obj.role,
					     id : obj._id
				     };
				     res.send({code: 1, message: 'Successful'});
			     }
				else
					res.send({code: 0, message: 'invalid password'});
			}
		});
	};


We have saved the role of the user in the session. Just storing the user id is more than enough to like the session to the user record but role is always required to show the content to the relevant user. So instead of fetching the user from the database using the userId in the session, it is always better to reduce external calls like the database calls, or the network calls

We will use a new route handler to do the role verification. Let us name that new handler to be `isLoggedIn` which will determine whether the user is loggedIn or not. If not logged in we will call the error middleware of express.

	function isLoggedIn(req, res, next) {
		// Checking if the session has a property called 'user'
		// which gets created during login
		if (req.session && req.session.user){
			// Call the next callback for this route 
			next();
		}else{
			next(new Error('You are not permitted to access this'));
		}
	}
	
	app.get('/dashboard', isLoggedIn, dashboard);

Similarly we can create another route handler to differentiate 'admin' from everyone. For example, while getting the list of users 

	function isAdmin(req, res, next) {
		if (req.session && req.session.user && req.session.user.role === 'admin'){
			next();
		}else{
			next(new Error('You should an admin to access this page'));
		}
	}
	
	app.get('/users', isAdmin, getUsers);

You can realize by now that any new functionality can be easily plugged in as a new route handler.

[1]: http://www.noplug.in/blogs/introducing-express
[2]: http://www.noplug.in/blogs/express-sessions
[3]: http://www.noplug.in/blogs/using-mongodb
[4]: http://www.noplug.in/blogs/jade-templates
[5]: http://www.noplug.in/code