---
layout: default
title:  "Setup MongoDB"
date:   2014-04-10 10:10:00
categories: main
tags: ['MongoDB']
---

MongoDB is the most popular NoSQL database available. You can install it from [MongoDB site][2] and the [guide][1] for installation is also provided. I strongly suggest you to spend atleast an hour in configuring the server in your respective platform.

<!--more-->

I am using a OSX platform so I am not dealing with Windows specific commands. But add the bin folder of the mongodb to the system path for the terminal to identify the mongo executables.

After running the server with following command

	sudo mongod --dbpath=~/db/mongo/data

we will see the server running at port **27017**.

And mongo comes with a interactive javascript client called mongo shell. Lets use it to connect to the server and do some basic operations.

connecting to the server

	~$ ./mongodb/bin/mongo
	MongoDB shell version: 2.4.4
	connecting to: test
		
By default the database is `test` so when you display the value of db it shows test. Use `show dbs` to display the list of databases. Select a database with `use <db-name>`. Similar to the tables in the relational databases we have **collections** here. Unlike SQL databases you do not need to spend time on creating a database or creating a table the first time you insert something.

	> use mongoTest
	switched to db mongoTest
	
	> db 
	mongoTest
	
	//Insert a record
	> db.users.insert({name:'Prakash', age: 26})
	> db.users.insert({name:'Pradeep', address:'New York'})
	
As you see the insert command above the schema of the both the user records is not the same. As MongoDB is schema less or saying dynamic schema makes more sense as schema is required for anything.

So now we shall see how to use read a record from db. Lets display all the users with `db.users.find()` and to display as a json add `.pretty()` to it.


	> db.users.find()
	{ "_id" : ObjectId("5336bb40052b6a33c6e819fc"), "name" : "Prakash", "age" : 26 }
	{ "_id" : ObjectId("5336bc77052b6a33c6e819fd"), "name" : "Pradeep", "address" : "New York" }
		
	> db.users.find().pretty()
	{
		"_id" : ObjectId("5336bb40052b6a33c6e819fc"),
		"name" : "Prakash",
		"age" : 26
	}
	{
		"_id" : ObjectId("5336bc77052b6a33c6e819fd"),
		"name" : "Pradeep",
		"address" : "New York"
	}

If the above query is similar to a `select * from <table_name>` we shall now see how to select the fields to be displayed and setting query conditions.
	
	//Selecting the users with name as 'Prakash'
	> db.users.find({name : 'Prakash'})
	{ "_id" : ObjectId("5336bb40052b6a33c6e819fc"), "name" : "Prakash", "age" : 26 }
	
	//Get the address of the users whose name is 'Pradeep'
	> db.users.find({name : 'Pradeep'}, {address:1}) 
	{ "_id" : ObjectId("5336bc77052b6a33c6e819fd"), "address" : "New York" }
	
Expression `{address:1}` says that **address** column is to be displayed.
Lets try something like updating a record by adding a column. 

	//Update the user whose name is Prakash with address as 'India'
	> db.users.update({name:'Prakash'},{$set:{address:'India'}})
	
	> db.users.find({name : 'Prakash'})
	{ "_id" : ObjectId("5336bb40052b6a33c6e819fc"), "address" : "India", "age" : 26, "name" : "Prakash" }


Finally, to remove a record

	> db.users.remove({name:'Prakash'})
	> db.users.find()
	{ "_id" : ObjectId("5336bc77052b6a33c6e819fd"), "name" : "Pradeep", "address" : "New York" }

Mongo api is highly intuitive. Once you got a hang of how the Mongo stores the records, collections, and the basic transactions in here and all the research you have done on it :) Go through the [using Mongo in NodeJS][3]

[1]: http://docs.mongodb.org/manual/installation/
[2]: http://www.mongodb.org/downloads
[3]: http://www.noplug.in/blogs/using-mongodb