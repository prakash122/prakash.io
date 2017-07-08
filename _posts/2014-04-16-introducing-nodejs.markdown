---
layout: default
title:  "Introducing NodeJS"
date:   2014-04-16 11:50:00
categories: main
tags: ['NodeJS']
---

# Introducing NodeJS
***

##Javascript
Javascript is a dynamically typed scripting lanuguage which supports [functional][1], [imperative][2] programming paradigms. It is a multi-paradigm programming language

## NodeJS

NodeJS is a software platform to write standalone javascript applications popular for server-side. It uses Google’s V8 engine to execute the js code mainly designed for non-blocking I/O and asynchronous events. NodeJS applications run in single thread but all the I/O calls or network calls will be done in different threads which are managed by Node.

Few common concepts used in NodeJS which you will also come across in my posts.

* **Event driven programming**
	In event driven applications, the flow of the application is controlled by events like user clicks, client requests.
* **Non Blocking and asynchronous I/O**
Usually, programming languages perform I/O operations or even a network calls different threads but there is no concept of threading in javascript applications so Node is equipped with some core modules(http, fs) to handle these tasks which
* **Functional Programming** Javascript is a functional programming language. Here functions can be passed as parameters to functions, functions can have member functions, member fields.

#### Intallation
Install your node platform from [NodeJS website][3]

## Node Command Line
Node installation gives a command line utility. Any javascript code can be executed here. For example

	~$node
	> 10 + 2 * 2
	14
	> var a = 5
	undefined
	> a
	5

You can go ahead and try loading any node modules in the same way. Let us use **http** module which is a core module of NodeJS. you can start using all the functionalities of the module with the instance variable created.

	> var http = require('http')
	> http.globalAgent
	{
		domain: null,
		_events: { free: [Function] },
		_maxListeners: 10,
		options: {},
		requests: {},
		sockets: {},
		maxSockets: 5,
		createConnection: [Function]
	}
Don't go into too much detailing about the above command, it is only to show that the module is loaded. You might want to ask about using custom modules, and we will look at installing new modules once we get **npm** introduced.


## Starting to sprint
Let us see how to create simple web server with native http module and later extend it to deliver the contents of a local file.
The below code is will create a basic web server taken from a NodeJS site

	var http = require('http');
	var requestListenerFn = function (req, res) {
		res.writeHead(200, {'Content-Type': 'text/plain'});
		res.end('Hello World\n');
	};

	var server = http.createServer(requestListenerFn)

	server.listen(1337, '127.0.0.1');
	console.log('Server running at http://127.0.0.1:1337/');

Copy the above contents to the file **app.js**. Run the node server with the command line utility as below.

	MyNodeProject-$node app.js
	Server running at http://127.0.0.1:1337/

Open your browser and navigate to [**http://127.0.0.1:1337**](http://127.0.0.1:1337) or [**http://localhost:1337**](http://localhost:1337), you will see "**Hello World**".

Now, as we decided to extend this to deliver contents of a local file we will create a local file with name **local_file.txt**.

	MyNodeProject-$ cat > local_file.txt
	Hello there,
	How are you doing?
	^C

	MyNodeProject-$ ls
	app.js		local_file.txt


We will use **fs** module to read the local file just created and send the contents to the user. **fs** is another core module of NodeJS which lets you access the file system. Copy the below contents to app.js and run the server again with "**node app.js**"

	var http = require('http');
	var fs = require('fs');

	http.createServer(function (req, res) {
		fs.readFile('local_file.txt', function(err, data){
			if(err){
				res.writeHead(500);
				res.end('Error while reading the file : ' + err.message);
			}else{
				res.writeHead(200, {'Content-Type': 'text/plain'});
				res.end(data);
			}

		})
	}).listen(1337, '127.0.0.1');

	console.log('Server running at http://127.0.0.1:1337/');

Refresh your page you will see the contents of the file in your browser.

To understand more about the usage of readFile function you will need to understand about [callbacks][4].

Now go have a look at [Express][5]. And of course, there are many frameworks that have come up recently to ease your task for building a scalable web applications.

[1]: http://en.wikipedia.org/wiki/Functional_programming
[2]: http://en.wikipedia.org/wiki/JavaScript#Imperative_and_structured
[3]: http://nodejs.org/download/
[4]: http://www.noplug.in/blogs/js-callbacks
[5]: http://www.noplug.in/blogs/introducing-express
