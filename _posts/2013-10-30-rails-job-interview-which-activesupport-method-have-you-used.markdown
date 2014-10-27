---
layout: post
title: "Rails job interview - Which ActiveSupport method have you used?"
date: 2013-10-30 13:40
comments: true
categories: ["Rails", "Jobs", "Learning", "ActiveSupport"]
---

Ok, I know what you are thinking this is a very simple question; I also
thought that when I got asked the question but my answer point me out to
a place that most  people just neglect to acknowledge. 

It's turns out that I did not know the correct answer. My answer was
**String#capitalize** which is obviously wrong because as you can see in my
definition that method belong to **String** not to ActiveSupport. Probably
what I should do is to go to ActiveSupport's documentation and read all
the methods and memorize them, right? Well that's probably won't help me in
understanding what a bunch of methods can help me to solve a particular
problem. So I will take a different approach I decided to learn everything from this library and
here is my first take on this adventure. 

What I'll try to do is to mimic the library functionality I will try to share
the code and my notes in here. I will follow the API documentation in
alphabetical order so I don't miss anything. The first obvious choice will be
**Array** and the first method will be **Array#wrap**. *Wraps its argument in an array unless it is already an array (or array-like).*

My first iteration was pretty straight forward but even if the small set of examples 
that I wrote are passing I don't think that's the correct code for this method. 

``` ruby 
class Array
  def wrap(obj)
    return [] if obj.nil?
    if obj.respond_to? :to_ary
      obj.to_ary 
    else
      self << obj 
    end
  end
end

describe Array, '#wrap' do 

  describe 'wraps its argument in an array unless is an already an array' do 
    context 'if the argument is nil' do 
      it 'returns an empty list' do 
        expect([].wrap(nil)).to be_eql []
      end
    end

    context 'if argument respond to_ary' do 
      it 'returns to_ary resulted invocation' do 
        expect([].wrap([1,2,3,4])).to be_eql [1,2,3,4]
      end
    end

    context 'if argument doesnt respond to_ary' do 
      it 'returns an array with the argument as its single element' do 
        expect([].wrap(0)).to be_eql [0]
      end
    end

  end

end
```

I will do more iterations on this particular method before moving on; you can
keep track on my progress here: [Mimic](http://github.com/cored/mimic) which is the
place I will be uploading all my experiments. This is not a new idea of any
sort, people been learning stuffs this way for a lot of time; I just hope is
works also for me. 

## What I learned?

  * to_ary - Make an implicit conversion to the object
