---
layout: post
title: "Code Club: Reading Dotenv part 1"
date: 2014-07-14 19:39
comments: true
categories: ["CodeClub", "Dotenv"]
---

This is my first post on a series regarding code reading; read
[this](http://cored.github.io/blog/2014/07/11/lunch-and-learn-reading-code-good-by-saron-yitbarek/)
for more information. I picked up [Dotenv](https://github.com/bkeepers/dotenv)
to start with this adventure. 

I started with the [dotenv.rb](https://github.com/bkeepers/dotenv/blob/master/lib/dotenv.rb) 

```ruby
require 'dotenv/parser'
require 'dotenv/environment'

module Dotenv
  def self.load(*filenames)
    with(*filenames) { |f| Environment.new(f).apply if File.exist?(f) }
  end

  # same as `load`, but raises Errno::ENOENT if any files don't exist
  def self.load!(*filenames)
    with(*filenames) { |f| Environment.new(f).apply }
  end

  # same as `load`, but will override existing values in `ENV`
  def self.overload(*filenames)
    with(*filenames) { |f| Environment.new(f).apply! if File.exist?(f) }
  end

protected

  def self.with(*filenames, &block)
    filenames << '.env' if filenames.empty?

    filenames.inject({}) do |hash, filename|
      filename = File.expand_path filename
      hash.merge(block.call(filename) || {})
    end
  end
end
```

## What did I learn from this piece of code? 

The most tricky part would be the method with the inject code, but reading it
closer was easy to figure out. It's creating a new hash with the executed code
from the blocks above. Also it have a good trick for guarding against nil using
the ```|| {}``` construct. 

## How was the experience?

Actually at it was so small piece of code, I ended up feeling good about doing
this and wanted to keep on it, but I just thought to myself that I think is
better to start small and keep moving into a bigger amount of time later on;
I really don't want to spoil the habit before starting on it ;-)
