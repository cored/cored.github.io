---
layout: post
title: "Lunch and Learn: It's not your test framework is you by Robbie Clutton and Matt Parker"
date: 2013-10-08 12:32
comments: true
categories: ["Lunch-n-Learn", "BDD"] 
---

<iframe width="560" height="315" src="//www.youtube.com/embed/j7An19XQwBg"
frameborder="0" allowfullscreen></iframe>

## What is this talk about?
  * BDD
  * Brittle tests 
  * Dev written acceptance criteria
  * Unreadable test 
  * Flickering tests 
  * Hidden complexity
  * Understands what product owner wants and how to do it 

## What I've learned?
  * Cargo culting: using whatever is outside without understanding it 
    * When to use Gherkin
      * State diagrams for the masses
      * Given (start state)
      * When (event)
      * Then (state transaction)
    * Copy/Paste from tracker to editor
    * Living executable documentation 
  * What does make us feel pain doing BDD
    * Brittle tests: A single change in the application causes many tests to break and you have to visit all the places of the application to fix that 
      * Don't reveal intent.
    * Create helper methods that get use accross the test suite 
      * Build your own DSL representing your language domain
  * Product owners don't collaborate on acceptance criteria
    * It's hard
  * Allow the product owners use their language about the business and transport that to the test suite
  * Expose the features 
    * cucumber -f html 
    * http://relishapp.com
    * wally 
  * Flickering tests 
    * Use @quarantine 
    * cucumber --tags @quarantine
  * Delete tests that are not longer providing value
  * The maintenance required to keep a test running weighs against its value in the further development
  * Don't repeat steps process
    * Tag an step with a before hook and stub that implementation out
    * That particular implementation is already been tested in another feature/scenario
  * Stop driving things through the UI
  * Try to keep your build time down a minute
  * Find the pain 
  * Reveal intent 
  * BDD like you mean it 

## What did resonate with me about this topic?
Well this was a refreshing point of view for me, just because I've been trying
to stop testing particular pieces of an UI when I'm developing Rails
applications. I think there's a misconception in the Rails community about
acceptance tests as the presenters point out in the talk, what's is more
valuable from a business point of view is the domain logic of their particular
business not how exactly it's gets display in an UI; which are those concepts?
Well talking with the product owner will reveal that and will show us the way
to strive to a good solution. 
  
## Other resources
[Specification by Example](http://specificationbyexample.com/)
