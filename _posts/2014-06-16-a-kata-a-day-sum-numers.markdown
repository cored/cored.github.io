---
layout: post
title: "A kata a day: Sum numbers"
date: 2014-06-16 18:47
comments: true
categories: ["CodingKata", "Ruby"]
---

The kata for today is quite simple but following alone with the TDD cycle show
me some wrong assumtions that I've made; without further ado here is the
problem statement: 

```
Sum an arbitrary lenght of items.
```

Pretty straight forward statement, here is my first spec and implementation: 

```ruby
describe Sumer do 
  context '#call' do 
    it 'sums an arbitrary amount of numbers' do 
      expect(Sumer.(1,2,3)).to eql 6
    end
  end
end
```

```ruby
module Sumer 
  extend self

  def call(*items)
    items.inject(:+)
  end
end
```

I started to use this type of implementation following
[RubyLove](http://rubylove.io) style; because actually I'm not handling state
for this type of operation is just input get into the function and an output
get thrown out. All good; now let's make things a little bit difficult what
would happen if I sent an string in the items?

```ruby
describe Sumer do 
  context '#call' do 
    it 'sums an arbitrary amount of numbers' do 
      expect(Sumer.(1,2,'3')).to eql 6
    end
  end
end
```

So as I expected I can't use ```+``` with ```Strings``` and ```Fixnum``` so
I have to change my implementation. 

```ruby
module Sumer 
  extend self

  def call(*items)
    items.map(&:to_i).inject(:+)
  end
end
```

So far so good; but what will happen if I happen to pass a character to that
method? What I found out is that Ruby coerce any character to 0 calling it's
to_i method so that's a realive. But what would happen if I pass an
Array well I found a problem with my implementation and now I need to
fix it.

```ruby
module Sumer 
  extend self

  def call(*items)
    items.select { |item| item.respond_to? :to_i }.map(&:to_i).inject(:+)
  end
end
```

Now I'm green again. This doesn't look to me as a very elegant implementation;
I think I'm breaking the [Law of Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter)
So that leads me to make some research and found an explanation from a great
book from [Sandi Metz](http://www.sandimetz.com); in the book she says that you
are not breaking the law as long as you are working on the same data structure;
so as you can see at first I'm working against an ```Array``` and using the
```#select``` method is returning another ```Array``` and then ```#map``` is
returning another ```Array``` and at the very end is when I switch out to
returning a single value. So far so good :-) 

# What I've learned from this Kata?
* Calling to_i to ```String``` in Ruby return 0
* Always test the smaller happy path scenario
* The law of demeter is only break if the data structure in the chain changes

