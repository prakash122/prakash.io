---
layout: default
title:  "Jade Templates"
date:   2014-03-18 13:00:00
categories: main
tags: ['NodeJS', 'Jade']
---

#Jade Templates
***


In the usage of creating jade I will create a login and register pages using bootstrap and a slight javascript to make the server post and get an ajax call. 


####Layout.jade
      doctype html
      html
        head
          title= title
          link(rel='stylesheet', href='/stylesheets/style.css')
          link(rel='stylesheet', href='/stylesheets/bootstrap.css')
          script(type="text/javascript", src="/javascripts/jquery.min.js")
          script(type="text/javascript", src="/javascripts/bootstrap.js")
          script(type="text/javascript", src="/javascripts/script.js")
        body
          block content

####Login.jade

	extends ../layout
  
    block content
      #saved-content
  
      form(role='form', method='POST')
        .form-group
           label(for="email") Email address
           input#email.form-control(type="email", name="email", placeholder="Enter email")
        .form-group
           label(for="password") Password
           input#password.form-control(type="password", name="password", placeholder="Enter Password")
  
        button.btn.btn-default#submit-login(type="submit") Submit

####Register.jade

    extends ../layout
    
    block content
      #saved-content
    
      form(role='form', method='POST')
        .form-group
           label(for="email") Email address
           input#email.form-control(type="email", name="email", placeholder="Enter email")
        .form-group
           label(for="name") Name
           input#name.form-control(type="text", name="name", placeholder="Enter name")
        .form-group
           label(for="phone") Phone
           input#phone.form-control(type="number", name="phone", placeholder="Enter phone")
        .form-group
           label(for="password") Password
           input#password.form-control(type="password", name="password", placeholder="Enter Password")
    
        button.btn.btn-default#submit-btn(type="submit") Submit

