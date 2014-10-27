---
layout: post
title: "Is this a God Object?"
date: 2014-04-15 16:30
comments: true
categories: ["Ruby", "Rails", "OOP"]
---
This is a deep question probably for many, probably not. But I'm just writing it for my future me. How to know when you are dealing with a God Object this is from the Rails jargon not exactly that I want to focus in any piece of Ruby code. First let me make Google define what is a God object: In object-oriented programming, a god object is an object that knows too much or does too much. The god object is an example of an anti-pattern.

That definition seems kinda vague, probably we keep adding stuffs to our ```User``` model and keep telling ourselves that it doesn't do too much or that it doesn't know too much. Here I came with some items that will hint you if you have a God Object in case you don't want to accept it. 

* ```public```: Why is the use of this a hint? Well that implies that probably you don't know where your public interface starts and where does it ends which should hints you that your class is doing too much. 
* ```private```: Not exactly the use of it; but use it more than once? Yes I've seem that. 
* Lines of code: This is a no brainer 
* Could you name what is it that your class do without using 'and's if not then probably your class is doing too much
* More than 6 lines methods
* More private methods than public ones

This are just some of the things I can see at a glance; there are ways to solve some of this problems notably this article talks about it: 

* [7 Patterns to refactor fat ActiveRecord Models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/)

If I'm missing any heuristic for finding God Objects, please do share them in
the comments.
