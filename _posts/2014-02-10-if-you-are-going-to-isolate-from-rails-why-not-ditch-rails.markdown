---
layout: post
title: "If you are going to isolate from Rails why not ditch Rails?"
date: 2014-02-10 18:59
comments: true
categories: ["Rails", "Jobo", "Architecture"]
---

This was an question that [@gotjosh](http://github.com/gotjosh) asked me today and is a very valid one. But first let me give you some background on the discussion. 

I've been working on my new project [Jobo](http://cored.github.io/blog/2014/01/31/starting-a-project-by-myself/) and he notice that I'm starting to add a service layer to the mix. So then it came the question. If you are isolating that much from Rails why are you still using Rails? I will try to answer in here. 

## Rails is good for what it does

For starters, Rails is quite good for what it does. And what is that, you may ask? Well putting a web page in front of a database. 

## AR is good for accessing data and defining relations. 

What I'm putting inside my service layer is mostly an explicit API of what I'm using from AR and also it will contain business logic. AR is quite good for accessing data inside a database and also for defining relationships which is for what I'm going to use it. 

## I don't want to reinvent the wheel for this project. 

Probably at some point I will like to build my own ORM or even framework; "I think I did that in a job I had", but right now this is a project for experimentation in others areas in particular architecture and workflow of developing features, I really don't want to work on a new ORM for that I'm waiting for [Rom-RB](http://github.com/rom-rb). 

Maybe the problem is the structure I'm giving to my service layer, I'm just experimenting and not following the [Prime Stack](http://words.steveklabnik.com/rails-has-two-default-stacks) describe by Steve Klabnik; probably this could find others problems for new comers to the project. Hopefully I can explain to any new contributor how to follow alone. I will try to explain my reasoning behind this structure in another post. For now I was just trying to clarify a high level intention of why I'm using what I'm using and why I'm doing it this way.
