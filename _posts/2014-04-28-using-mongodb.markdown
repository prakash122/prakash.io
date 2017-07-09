---
layout: default
title:  "Using MongoDB"
date:   2014-04-28 09:30:00
categories: main
tags: ['MongoDB']
--- 

As we have looked at MongoDB earlier about how to [setup][1] and start using the command line interface we shall now see how to use it with NodeJS.

<!--more-->

Connecting to Mongo 
Mongoose

#### Theory
* Schemas
* Models

#### Implementation
* Connecting
* Querying
* Populating


### Mongoose
Mongoose is the most popular ORM for MongoDB in NodeJS platform. 

#### Schema
For using any ORMs you will need to define a schema. You will need to define all the fields required by the model. Even while using a schema the MongoDB schema can be dynamic but all the fields or columns you are going to use in the MongoDB should be available in the Mongoose Schema.

	var mongoose = require('mongoose');
	var Schema = mongoose.Schema;
	
	var UserSchema = new Schema({
		name: String,
		email : String,
		phone : Number,
		username : String,
		password : String,
		role : String //admin, manager, employee
	});
	
	mongoose.model('User', UserSchema);
	
#### Data Types


#### References


### Implementation
To explain the who implementation I shall use few of the code snippets above

#### Population
This is an interesting addition to querying. 
MongoDB does not support joins like we have in all SQL counterparts. Mongoose has provided us nice facility to reference a record of a collection to another record of same or different collection.
Let us see how to implement this and how that can be useful. For explaining this, we will take a simple use-case of creating a manager

	// column holding the reference 
	


[1]: http://www.noplug.in/blogs/init-mongo