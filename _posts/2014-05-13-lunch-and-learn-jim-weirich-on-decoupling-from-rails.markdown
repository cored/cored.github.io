---
layout: post
title: "Lunch and Learn: Jim Weirich on Decoupling from Rails"
date: 2014-05-13 16:11
comments: true
categories: ["Rails", "Lunch-n-Learn", "OOP"]
---

<iframe width="560" height="315" src="//www.youtube.com/embed/tg5RFeSfBM4" frameborder="0" allowfullscreen></iframe>

## What is this talk about?
* Hexagonal Rails in practice
* Iterating to better architecture in Rails application
* Repository pattern 
* Extracting business logic from models 
* What should we put into models

## What did I learn from this talk?
Jim's talk are always delightful; but most of the things here I've been implementing it already on some of the projects I've been working on. The thing that catch my attention the most was creating a proxy object to handle actual business logic which I never thought about and looks lie a viable technique. At first I was thinking that there was a lot of abstraction for this particular implementation in particular with the ```Repository``` object I think I'll still would use the active record models as repository without any business logic and will introduce the new entities separated from Rails to see how that works for me; why you may ask? Well I was suffering from [Anemic Domain Model](http://www.martinfowler.com/bliki/AnemicDomainModel.html) where most of the business logic was segregate in a lot of services objects without any obvious relationship between them. 

In the end probably not all the things that Jim's teaches are good for most Rails projects I for one know that is not probably a good fit for DHH and Basecamp at this time. For me this techniques help us to easy resonate about a code base and that always is helpful to build solid applications.  
