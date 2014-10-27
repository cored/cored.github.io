---
layout: post
title: "A kata a day: Substring counter"
date: 2014-08-18 20:06
comments: true
categories: ["CodingKata", "Ruby"]
---

# Problem statement

Complete the solution so that it returns the number of times the search_text is 
found within the full_text.

# Specification

```ruby
describe SubstringCount do
  describe '#count' do
    it 'returns the proper count for a substring consisting of an individual character' do
      expect(SubstringCount.('aaaa','a')).to eql 4
    end

    it 'returns the proper count for a substring of more than one character' do
      expect(SubstringCount.('aa_bb_cc_dd','aa')).to eql 1
    end

    it 'returns proper count for a longer string' do
      expect(SubstringCount.('aaabbbcccc', 'bbb')).to eql 1
    end
  end
end
```

# Solution 

```ruby
module SubstringCount
  extend self

  def call(full_text, search_text)
    full_text.scan(search_text).size
  end

end
```

# What did I learn from this kata?

[String#scan](http://www.ruby-doc.org/core-2.1.2/String.html#method-i-scan)
