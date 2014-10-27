---
layout: post
title: "Thoughts: Another way of doing Hexagonal Rails"
date: 2013-09-17 15:23
comments: true
categories: ["Rails", "Architecture", "Hexagonal", "OOD", "Thoughts"]
---

A couple of months ago I watched this talk [Hexagonal Rails](http://www.youtube.com/watch?v=CGN4RFkhH2M) by Matt Wynne the author of
the Cucumber book. If you haven't see it go watch it, I'll wait. 

As I was saying there are a lot of great tips there and it will make you think
about OOP and how you are developing your Rails applications, but there was something 
in particular that took my attention and it was the way Matt was handling the
logic for faiulres/success for a particular action inside the controller [Omar
Rodriguez](http://github.com/orodriguez) came with a good idea using modules
and including the module logic within the controller; I just move it to a new
class with just one action like the following:

<script src="https://gist.github.com/cored/6599407.js"></script>

I think this is a better approach just because the controller that you are
trying to remove logic from will end up with a lot of methods inside it and in
this way the code is exactly where it should be in a piece of knowledge part of
the action. 
