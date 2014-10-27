---
layout: post
title: "Job interview - Rails main components"
date: 2013-10-29 08:08
comments: true
categories: ["Jobs", "Rails"]
---

This is my series of blog posts regarding the questions that I found in some
interviews that I've been having; I will let the reader the judge the
effectiveness of this questions. My only intention with the series is to show
the correct answer for the questions. Without further ado:

# Which are Rails main components?

For some this questions probably could be kinda confusing; most newbies will
think that the components of the Rails framework are MVC, but that's far away
from the truth just because that's just a design pattern on which the Rails is
based. The actual answer for this question is quite simple:

* ActiveSupport is a collection of utility classes and standard library
extensions that were found useful for the Rails framework. These additions
reside in this package so they can be loaded as needed in Ruby projects outside
of Rails.
* ActiveRecord connects classes to relational database tables to establish an
almost zero-configuration persistence layer for applications.
* ActiveModel provides a known set of interfaces for usage in model classes.
* ActionView is a framework for handling view template lookup and rendering, and
provides view helpers that assist when building HTML forms, Atom feeds and
more.
* ActionPack is a framework for handling and responding to web requests. 
* ActionMailer
Action Mailer is a framework for designing email service layers. 

I have to admit that I got short on my definition for this particular question.
For instance I left out ActionMailer and ActionModel. But now I know which are
the main components, and hopefully somebody will learn it also. 

## Resources

[Rails](http://github.com/rails/rails)

[Preparing for Ruby on Rails job interviews](http://www.psteiner.com/2013/02/preparing-for-ruby-on-rails-job.html)

[Ruby on Rails Questions](http://blog.sandeep.me/2012/05/ruby-on-rails-interview-questions.html)
