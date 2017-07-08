---
layout: default
title:  "Javascript Callbacks"
date:   2014-04-20 23:00:00
categories: main
tags: ['Javascript', 'Callbacks']
---

# Javascript Callbacks
***

### What are callbacks

From a wiki definition on [callbacks][1], a callback is a piece of executable code that is passed as an argument to other code, which is expected to call back (execute) the argument at some convenient time. 



### Javascript is Asynchronous language

Javascript is not a multithreaded language but is asynchronous with two concepts callbacks and events. 

Javascript came into existence to make the static web pages dynamic and take complete control of actions and events in a web page. As it is made for the web spawning multiple threads and waiting for web calls is quite a overhead on the browsers. So Javascript is made a single threaded language and all the web requests(animations were not so popular in the begining) are handled by the browser and once the response is obtained the browser will call back the application. So, all the web requests are asynchronous, similarly there are multiple APIs the browser provides to handle external tasks. To create a manual asynchronous execution of certain piece of code execute with setInterval and setTimeout which are ofCourse dependent on system clock. 

To simplify the above story short on what is expected of javascript is any thing that is made in pure javascript is executed synchronously and all the external calls will happen asynchronously and when required the browser or Node platform call the callback function. These asynchronous web requests are popularly termed as AJAX (Asynchronous Javascript As XML). 

Coming to events in javascript you see them a lot in javascript executing in the browser like click events, script loaded, page loaded etc., These will be triggered by the browser and listeners to the events will be registered in the javascript.

	//Example events in the web

	//To find an element by its id in Webpage HTML
	var el = document.getElementById("button");
	
	//To register for an event 'click' on the button 
	el.addEventListener("click", );	
	
Check an example code on registering to events in [JSFiddle example][2]

### Understanding the single threaded app


![Flow][3]
In the figure above, you can see that the javascript application is trying to do the following things

* a web request to get facebook user details
* a database call to get a user record 
* receive data on a web socket
* and more

Each of these tasks takes time as shown in the picture. In a traditional multi-threaded language, the flow of the application waits for the responses from each of these services. And so a practise of using them in a new thread is encouraged. 

Well, as you cannot create threads in javascript you rely on either the browser or the NodeJS platform to take up this task. Thus the app is not waiting any of these services to respond back. In turn, you register a callback and the platform calls back one the response from the respective service is obtained. To do this task the platform has to create multiple threads to handle these external tasks. So the above figure about the threads maintained by the platform and also the to show the time spent by the application alone is zero.


Following are few of the concepts which can introduce asynchronous behaviour to javascript.

 * setInterval
 * setTimeout
 * requestAnimationFrame
 * XMLHttpRequest
 * WebSocket
 * Worker
 * Some HTML5 APIs such as the File API, Web Database API


[1]:http://en.wikipedia.org/wiki/Callback_(computer_programming)
[2]:http://jsfiddle.net/prakash122/qveCC/
[3]:http://www.noplug.in/images/browser_thread_border.png
