---
layout: post
title: "Code Read: Dotenv part 2"
date: 2014-07-16 07:40
comments: true
categories: ["CodeRead", "Dotenv"]
---

Following with my series on reading code and keeping up with the
[Dotenv](https://github.com/bkeepers/dotenv) project; I notice something in my
first iteration, I started with the run file to start reading, here is why. 

I thought that figuring out where to start was going to be hard; but it was
kinda easy. I just have to find where the program gets it's initial input.
Which in the case of Dotenv would be: 

```ruby
#!/usr/bin/env ruby

require 'dotenv'

begin
  Dotenv.load!
rescue Errno::ENOENT => e
  abort e.message
else
  exec *ARGV unless ARGV.empty?
end
```

The ```dotenv``` executable inside ```/bin``` which was pretty obvioous but
I did not start from there so I will do an step back in this new article.

## What is this piece of code doing?

This piece of code is very explanatory of it's own so I will move to a topic
more interesting in my honest opinion; intent. 

## What is the intent of the programmer?

When I first start reading this piece of code I notice that the exception
handling looked at first as if it is doing control flow; but in fact it doesn't
just because the called method is a destructive method and it will do something
it will contain side effects for the object that's been call to. So in that
case it's looks legit to use that mechanism in here. Also that give the code
another benefit and is to handle the general case to show the help message
without a lot of ceremony which is good. 

The best part of this is that it does look very concise as it is. 

## What did I learn from this experience?

I think that what I learned was actually that sometimes exceptions are the way
to go for some control flow handling; yes I know I said that this is not
actually control flow but there is some change of branching in the entire
statement so is more or less as it is doing so but it's the way to go for this
particular case. The other thing that I notice was that it's starting to get
really fun to read code; don't know why actually but that's good for my own
motivation :-)

