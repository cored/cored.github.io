---
layout: post
title: "A kata a day: Simple Sentences"
date: 2014-08-25 21:14
comments: true
categories: ["CodingKata"]
---

## Problem Statement

Implement a function, so it will produce a sentence out of the given parts.

Array of parts could contain:
- words;
- commas in the middle;
- multiple periods at the end.

Sentence making rules:
- there must always be a space between words;
- there must not be a space between a comma and word on the left;
- there must always be one and only one period at the end of a sentence.

## Specs

```ruby
describe SimpleSentences do

  it 'add spaces between words' do
    expect(SimpleSentences.(['hello','world'])).to eql 'hello world.'
  end

  context 'commas in the middle of the array' do
    it 'doesnt add spaces between left word and comma' do
      expect(SimpleSentences.(['hello',',','cousin'])).to eql 'hello, cousin.'
    end
  end

  context 'dots' do 
    it 'returns only one dot at the end and not more' do 
      expect(SimpleSentences.(['hello', 'world', '.'])).to eql 'hello world.'
    end
  end

end
```

## Implementation 

```ruby
module SimpleSentences
  extend self

  def call(args)
    args.reject! { |item| item =~ /\./ }
    "#{args.inject("") do |sentence, item| 
      item =~ /\w+/ ? sentence << " #{item}" : sentence << "#{item}"
    end.strip}."
  end
end
```

## What did I learn with this kata?

I felt like this implementation was very straight forward and easy to
understand but actually there was an smaller and easier solution from another
user at [Codewars](http://www.codewars.com) which by the way is the site from
where I'm taking some of the new coding katas that I'm using lately; here is
the implementation from that user.

```ruby
parts.join(' ').gsub(' ,', ',').sub(/(\s\.)*$/, '.');
```

That's just beautiful, following the implementation step by step. 

* First he transforms the entire array into an string separated by spaces. 
* Second he do a global substitution to remove the space between a word and
  a comma. 
* The end of the expression was a little bit tricky at first but then it was
  obvious; he just find the last space and dot in the end of the string and
  replace it with just one dot. 

That implementation was just clever :-)
