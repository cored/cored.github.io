---
layout: post
title: "How to approach a old Rails code base"
date: 2014-04-16 20:27
comments: true
categories: ["Business", "Rails"]
---

Recently I have to work with a big code base and most of my approaches to deal
with were wrong. I started to reflect on what I did wrong and end up with
some guidelines for myself and maybe for others to help for doing good work.

* Stop refactoring stuffs that you don't understand: That's right, we tend to
  belive that we can extract logic from a method and put it elsewhere and that
  magically will give us more manageable classes. That's right in theory but in
  practice that's further from the truth. The reality is that we really don't
  know what the code is doing and is better to just add the new bug fix/feature
  without doing a lot of changes on code that is already working and also is on
  production. You can do some refactoring later on when you come back to this
  piece of code; because you will come back to it... don't worry.

* Stop extracting service objects, presenters, police objects, etc...: Wait
  a second I've been reading a ton of documentation regarding best practices,
  good oop design and arquitecture just to get better at scaling Rails apps
  now I have to stop? Well, yes and no it's better to stop for now. Not every startup
  out there is digging the new prime Rails stack and if they are fine with vanilla Rails 
  then you should work that way. You may ask that's not the only advantages of
  having isolated objects that interact with Rails; another advantage is fast
  tests. Well not everybody understand the benefits of having a bug, fixing it
  test driven; refactor that bug run the test again and have instant feedback
  without waiting for a deploy to an staging server to check that you made
  a mistake. Not everybody knows that the last D in TDD means 'Design' not
  'Development'; so if that's the case so be it. 

We get pay to give back value to the business that hired us but we first needs 
to know what is value for them. Maybe value for your company is just that you are 
closing the same bug because your code base is a mess and that's fine with them and 
probably it should be fine with you; but if you as myself want to keep improving on 
your craft you should just move on to another job and stop pushing that wall because 
it probably won't move anytime soon.
