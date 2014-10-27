---
layout: post
title: "What to refactor?"
date: 2014-02-17 17:18
comments: true
categories: ["Ruby", "Refactoring"]
---

I receive this question the other day by a friend at that moment I realize that I've never thought about it. How do I know where to start when refactoring? And how do I know is the right time? The second question is a little bit obvious the right time is any time. Refactoring is the art of getting your code in shape and you should be doing it often; but the first one is a little bit trickier and I will try to share some tips to consider when you are doing this skill. 

## Code Climate, reek, roodii, flay, etc...

Sometimes what you should do is just to use any code metrics tool and start refactoring what they suggest is bad code. 

* Advantages: Very quick to spot where are some problems.  
* Disadvantages: There are some things that this tools points out that probably are better left alone just because even when they are code smells is the best thing to do at that particular moment in time. 

## Private methods 

What, private methods? Why? Well this is a technique that I picked up from [Sandi Metz](http://www.amazon.com/gp/product/0321721330/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&tag=chamaxwoo-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321721330); probably she said it somewhere. She tries not to use private methods on classes; so I decide that when I see some private methods that's an indication that there's probably a concept hiding.

* Advantages: You probably will start to add new smaller classes which will brings more reuse of code.  
* Disadvantages: Sometimes you really don't need to extract a new class to use a gsub on something value. The fact that you will have more classes will implies that you will have some physical restructuration of your code base to find things faster.

## Follow your heart

Nop; this is not a joke you can always use your intuition to find where the code is smelly and just refactor on that.

* Advantages: You will feel like a human compiler
* Disadvantages: If you don't really know about code smells you probably will start to introduce some of your own.

## Resources

### Tools 
[Code Climate](https://codeclimate.com/?v=original)

[Roodi](https://github.com/roodi/roodi)

[Reek](https://github.com/troessner/reek)

### Books 
[Refactoring: Improving the design of existing code](http://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672/ref=sr_1_1?ie=UTF8&qid=1392671818&sr=8-1&keywords=refactoring)

[Refactoring in Ruby](http://www.amazon.com/Refactoring-Ruby-William-C-Wake-ebook/dp/B002TIOYVM/ref=sr_1_5?ie=UTF8&qid=1392671864&sr=8-5&keywords=refactoring+ruby)

 
