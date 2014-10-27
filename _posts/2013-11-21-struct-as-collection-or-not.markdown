---
layout: post
title: "Struct as collection or not"
date: 2013-11-21 09:30
comments: true
categories: ["Ruby", "OOD", "Thoughts"]
---

One of the things I normally do when doing any kind of programming is to wrap
every single data structure in my system in an object. Why you may ask? Well
basically it's just to give some semantic meaning to the internals of my
application without exposing too much of the language primitive types behavior.
But apart from the OOD obsessions I'm also kinda lazy and always looking a way
to reduce typing. That's when I start to experimenting with Structs for
wrapping data structures in particular collections. 

So basically if I have something like:

```ruby
class Words
  include Enumerable

  def initialize(words)
    @words = words
  end

  def each 
    return to_enum unless block_given?
    @words.each { |word| yield word } 
  end
end
```
I could write it like this: 

```ruby
  Words = Struct.new(:words)
```

Right? ... Wrong!!! This piece of code is not equivalent to the above code for some simple
reasons. Struct doesn't know that it's interacting with a list or a string or what have
you, it's just knows that it's have a member inside containing something. This have the
disadvantage of not let the client to manipulate the internal data structure through
a well define API for it, you can always define it yes but then why don't use the first
approach which also shows the person reading the code which data structure you are actually
using. 

So is Struct a bad thing? Not at all, I will use it just as meant to be as data
containers... Then again probably you are thinking but isn't an array a data 'container', yes it's  is but
if you recall what I said in the first place I want to give meaning to the internal of my
system so I know what's everything do just by reading it. So there you have it, I will keep using Struct but
just when is the right thing to do.
