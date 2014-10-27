---
layout: post
title: "A Kata a Day: Format Words or today I wrote the ugliest code ever"
date: 2014-07-16 20:00
comments: true
categories: ["CodingKata", "Ruby"]
---

This in part sad and in part good news, but let's get with the sad part first. 
As my daily kata building practice I try to find out a problem and work on it. 

Today's problem was the *Format Words* problem; here is the statement: 

```
Complete the method so that it formats the words into a single comma separated value. 
The last word should be separated by the word 'and' instead of a comma. 
The method takes in an array of strings and returns a single formatted string. 
Empty string values should be ignored. Empty arrays or null/nil values being passed into the method should result in an empty string being returned.
```

Pretty straightforward so I wrote a couple of specs for it: 

```ruby
require 'minitest/autorun'

describe FormatWords do 
  it 'returns a proper formatted word for an array of three words' do 
    FormatWords.(['ninja', 'samurai', 'ronin']).must_equal 'ninja, samurai and ronin'
  end

  it 'returns the same array for none words in it' do 
    FormatWords.([]).must_equal []
  end

  it 'returns a proper formatted word for two empty words and a word' do 
    FormatWords.(['','','three']).must_equal 'three'
  end

  ... 
end
```

Which leads me to a very crappy and ugly implementation, by the end of this
kata I did not have any energy left to refactor it I just look at it and start
to reflect on what happen. Here is the implementation: 

```ruby
def format_words(words)
  return "" if words.nil? 
  words = words.reject(&:empty?) 
  return "" if words.empty?
  return words[0] if words.size == 1
  temp = words[0..-2].map { |word| word unless word.empty? }.compact
  return "#{temp.join(', ')} and #{words[-1]}" if temp.size > 1
  "#{temp[0]} and #{words[-1]}"
end
```

## What did I learn about this kata? 

* [#reject](http://ruby-doc.org/core-2.1.2/Enumerable.html#method-i-reject):
  Which helps me to remove blank strings from the array
* Also this kata re starts a crusade that I have with TDD. I really want to
  know which are the proper tests to write which are the correct edge cases and
  actually I just realize I don't know that, yet. So I will start to research;
  that last part was the happy thing that happen ;-)


