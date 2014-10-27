---
layout: post
title: "Jobs Interview - More Ruby, Rails questions and answers"
date: 2014-02-12 12:07
comments: true
categories: ["Ruby", "Rails", "Jobs"]
---

Following my series on job interviews which started here [Job Interview - Rails Main Components](http://cored.github.io/blog/2013/10/29/rails-job-interview-qa/) This post will have new questions. Hopefully some people will learn a few things, at least I did. Without further ado the questions and answers; enjoy!. 

## Name at least 3 ways to call a method in Ruby? 

```
 class Greeter
    def greeting
       'hello' 
    end
 end 

greeter = Greeter.new 

# First way and most obvious 
greeter.greeting 

# Second way not so obvious but probably well known 
greeter.send :greeting 

# Third way the more obscure 
greeter.method(:greeting).call 
```

## What is the difference between a Class, Module and Instance?

* Class: is the blueprint from which individual objects are created. Classes in Ruby are first-class objects---each is an instance of class Class.
* Instance: is an object which was instantiated from a Class
* Module: is a collection of methods and constants. You can't make an instance of a module; the way you access the constants and methods inside it depends on it's definition; module methods and variables can be accessed like this `Module.method` or `Module.CONSTANT` but if you want to access an instance method you should include the module to another class to use it. 

## What is the difference between include and extend?

`include` mixes a module as instance methods or constants and `extend` mixes a module as class methods; also you can extend a singleton object instance using this syntax. 
`obj.extend Mod` which will add the methods or constants to that single instance of that particular class.

## What is a symbol and what are the risks of using them in web apps?

A symbol is mostly an immutable [String](http://www.ruby-doc.org/core-2.1.0/String.html) they are mostly use to represent names. They are risky to be use on web apps in particular in Rails apps due to the following security issue:[CVE-2013-1854 Symbol DoS vulnerability in Active Record]( https://groups.google.com/forum/#!topic/ruby-security-ann/o0Dsdk2WrQ0 ) 

**Update:** This cannot lead an attacker to execute arbitrary code in the server you are running your app. That was a misinterpretation on my part. Thanks to [Toby Ovod-Everett](https://github.com/tovodeverett) for pointing this out.

## Can you name a recent security incident in Rails and explain the issue?

This was not very easy for me; because I normally don't spend too much time on this type of stuff this days; even when I know the important of the topic; so the answer for this one is just to subscribe to the [rails-security-ann](https://groups.google.com/forum/#!forum/ruby-security-ann) mailing list 

## What does this do Hash[[1,2,3,4].zip([5,6,7,8])]?

**Enumerable#zip**  Takes one element from enum and merges corresponding elements from each args.

```
  {1=>5, 2=>6, 3=>7, 4=>8}
```

## What does the following do 

```
(1..Float::INFINITY).map{ |i| i*i }.first(10)?
```

Nothing it will just hang your Ruby interpreter. 

## How to fix this 
```
  (1..Float::INFINITY).map{ |i| i*i }.first(10)?
```

This only works on Ruby 2.0

```
(1..Float::INFINITY).lazy.map{ |i| i*i }.first(10)

```

## What is the difference between a Proc and Lambda?

* Block: Is a piece of code that can pass to a method as an argument. But can't save it's own state. 

* Proc: Is a block which can save state.

* Lambda: Same thing as a Proc; the differences are that the lambda have diminutive returns; which means that even if you put a *return* statement inside the lambda it will keep running until the method in which was called finish it's execution also lambdas check for the arguments passed to them; Proc doesn't. 

## Conclusion 

I hope this help anyone interesting in learning tip bits of Ruby. Also I also would love to see another methods to solve the problems exposed in this questions. Happy coding!

## Resources

[Ruby Object](http://ruby-doc.org/core-2.1.0/Object.html)

[What is a Class?](http://docs.oracle.com/javase/tutorial/java/concepts/class.html)

[Ruby Module](http://ruby-doc.org/core-2.1.0/Module.html)

[Ruby Class](http://www.ruby-doc.org/core-2.1.0/Class.html)

[Include and Extend](http://stackoverflow.com/questions/156362/what-is-the-difference-between-include-and-extend-in-ruby)

[Enumerable#zip](http://ruby-doc.org/core-2.1.0/Enumerable.html#method-i-zip)

[Enumerable#lazy](http://ruby-doc.org/core-2.1.0/Enumerable.html#method-i-lazy)

[Blocks, Procs, Lambdas](http://www.robertsosinski.com/2008/12/21/understanding-ruby-blocks-procs-and-lambdas/)
