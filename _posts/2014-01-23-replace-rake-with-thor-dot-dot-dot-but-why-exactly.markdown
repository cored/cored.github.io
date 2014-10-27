---
layout: post
title: "Replace Rake with Thor... but why exactly?"
date: 2014-01-23 20:52
comments: true
categories: ["Ruby", "Rake"]
---

I was reading the following article today [Replace Rake with Thor](http://codecrate.com/2014/01/replace-rake-with-thor.html?utm_source=rubyweekly&utm_medium=email) the author says the following:

> Okay, let's be honest, when was the last time you actually wrote a unit test for a Rake task?

My answer would be, I'll normally unit test my tasks because I don't put any
logic inside the Rakefile itself. So then again he says exactly that in his article as
well: 

> Most developers "work around" unit testing Rake tasks by extracting logic out of the 
Rake task and into an actual unit testable Ruby object, still leaving the actual Rake task untested. 

Which makes me ask, "why would I want to test the actual Rake task if all the
logic is inside another isolate object which I can unit test?" Probably
I should question this again.. "What is exactly the advantage of Thor over Rake
if I can do the same thing with the later and with a great DSL?". 

Let's rememeber what's exactly is Rake:

> Provides a simple build program with capabilities similar to make.

For any new programmer proabably that's kinda odd so; let's try to know
what's make:

> Make gets its knowledge of how to build your program from a file called the makefile, 
which lists each of the non-source files and how to compute it from other files. 
When you write a program, you should write a makefile for it, so that it is possible to use Make to build and install the program.

So parting from that definition it's seems to me that probably the author and
a bunch of other people is using Rake for the wrong type of things. The problem
in my opinion is not the fact that he is suggesting to switch to another less known tool 
for doing the same thing that we are already doing with Rake the problem is that his 
argument is just plain wrong because we can test the important logic of a Rake task in 
isolation without any real issue.
