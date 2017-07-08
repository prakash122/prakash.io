---
layout: default
title:  "Introducing ExpressJS"
date:   2014-04-10 17:50:00
categories: main
tags: ['NodeJS', 'JS', 'ExpressJS']
---

## Introducing ExpressJS
***

Express is a web application framework for NodeJS. It is a highly configurable feature-rich framework which does not restrict the project structure basing on architecture. There are lot more required libraries, [NodeFrameworks][1]

The documentation of [ExpressJS][1] is highly informative. So, what I want to cover here is how to start off an express based application, understand the middlewares you are going to use.

Make sure that the [Node][3] and [NPM][4] are installed.

Installing Express globally will give you a generator automatically. But I will suggesting installing the express generator which gives you a express binary. 

	~$npm install -g express-generator

While generating the app you can configure it with following options (taken from above mentioned express guide)
	
	Usage: express [options]
	
	Options:
	
		-h, --help          output usage information
		-V, --version       output the version number
		-e, --ejs           add ejs engine support (defaults to jade)
		-H, --hogan         add hogan.js engine support
		-c, --css   add stylesheet  support (less|stylus) (defaults to plain css)
		-f, --force         force on non-empty directory
	  
You can create an express app by typing express and sending the name of the project as the parameter. Since we added no options it generated scaffold uses plain CSS and Jade templates.


	~$express MyExpressApp

		create : MyExpressApp
		create : MyExpressApp/package.json
		create : MyExpressApp/app.js
		create : MyExpressApp/public
		create : MyExpressApp/public/stylesheets
		create : MyExpressApp/public/stylesheets/style.css
		create : MyExpressApp/routes
		create : MyExpressApp/routes/index.js
		create : MyExpressApp/routes/user.js
		create : MyExpressApp/public/javascripts
		create : MyExpressApp/views
		create : MyExpressApp/views/index.jade
		create : MyExpressApp/views/layout.jade
		create : MyExpressApp/views/error.jade
		create : MyExpressApp/public/images
		create : MyExpressApp/bin
		create : MyExpressApp/bin/www

		install dependencies:
		$ cd MyExpressApp && npm install

		run the app:
		$ DEBUG=my-application ./bin/www

	
Now install the dependencies and start running the app
	
	~$cd MyExpressApp
	MyExpressApp-$npm install
	MyExpressApp-$npm start
	
Go to browser and open [`http://localhost:3000`][5]. There you are, you first Node Express Server is up and running. You may want to look at multiple [express examples][6]. 


## Dwelling into app

Lets the file `app.js` which is the starting point of your server. Though, there are multiple configurations available in Express I will discuss only the important parts of it. You can read the rest from the Express guide.

After loading the importing the required node modules, get the instance of Express server from `express()` we shall look at the following configurations
	
	//Letting the express know where the views are available
	app.set('views', path.join(__dirname, 'views'));
	
	//Setting the view engine to jade
	app.set('view engine', 'jade');


### Middlewares

Lets look at the middlewares. Every `app.use()` function call registers a new middleware. So, when a server receives new request first middleware is called and if the request can be answered the request will be answered else it will be forwarded to next middleware by invoking `next()`, usually used to call the next listener
	
	var favicon = require('static-favicon');

	//Gets the webpage's fav-icon which is the first middleware
	app.use(favicon());
	
	// Logger, for logging the server requests
	// Morgan is a logging library from ExpressJS
	var logger = require('morgan');	
	app.use(logger('dev'));

#### Body Parsers	

Body parsing middleware. Parsing the body of server requests which are in JSON format like the POST data will be handled by a middleware `bodyParser.json()`. Similarly encoded url will be decoded by another middleware `bodyParser.urlencoded()`
	
	var bodyParser = require('body-parser');
	app.use(bodyParser.json());
	app.use(bodyParser.urlencoded());

	
#### Cookies

Parse Cookie header and populate `req.cookies` with an object with cookie names as keys. So, in your controller to access the user cookies it is just about looking into `req.cookies`
	
	var cookieParser = require('cookie-parser');
	app.use(cookieParser());

#### Static Files

All the static files which should be publicly accessable can be served using `express.static()` middleware. So every api request will go through this middleware and see if the requested path is on the given static path of this disk. So, there will be a lag of atleast 1ms for the server to respond over all. So one way of handling this can be via setting a prefix for accessing static files, check the following code snippet. All the public files need to be prefixed with `/static` like **http://localhost:3000/js/script.js** to **http://localhost:3000/static/js/script.js**

	app.use(express.static(path.join(__dirname, 'public')));
	
	//Setting a prefix value
	app.use('/static', express.static(path.join(__dirname, 'public')));

Refer [SO Answer][7] for more information on static files and its order compared to router

#### Router and Routes

Routing of the different web request to their respective controllers will be handled by the `app.router`. If none of the above middlewares cannot address the user request then `app.router` will check all the routes registered with it and if any route matches the request url then the respective callback will be called.

	app.use(app.router);

	// Place where u have your controllers
	var routes = require('./routes');
	var users = require('./routes/user');
	
	// Registering the routes
	app.get('/', routes.index);
	app.get('/users', users.list);


If none of the above middlewares are not able handle the user request, it means the path user request is not found. So, our next middleware is trying to respond with **Not Found** or **404** response.

	app.use(function(req, res, next) {
    	var err = new Error('Not Found');
		err.status = 404;
    	next(err);
	});

#### Error Handling

We can have a middleware which will always be available to handle the errors in the application. So this will be given in the end of all the middlewares. The arity of this middleware is 4 with signature `function(err, req, res, next)` So, from any middleware above if there is an error you want to throw just call `next(err)`. From the below snippet you will find 2 error middlewares one in the development mode to get the full error stack trace and other in the production to just display the error message.

	// development error handler
	// will print stacktrace
	if (app.get('env') === 'development') {
	    app.use(function(err, req, res, next) {
	        res.render('error', {
	            message: err.message,
	            error: err
	        });
	    });
	}
	
	// production error handler
	// no stacktraces leaked to user
	app.use(function(err, req, res, next) {
	    res.render('error', {
	        message: err.message,
	        error: {}
	    });
	});

As you see these are list of middlewares provided by Express but it is up to us, we can create new, delete the existing, replace with a better alternative. For Example, check [flatiron][8] for alternatives to these Express middlewares and you can use any of those middlewares replacing the ones provided by Express.

[1]: http://www.nodeframework.com
[2]: http://expressjs.com/guide.html
[3]: http://nodejs.org/download/
[4]: http://noplug.in/blogs/NPM
[5]: http://localhost:3000
[6]: https://github.com/visionmedia/express/tree/master/examples
[7]: http://stackoverflow.com/a/12695813/557978
[8]: http://flatironjs.org/