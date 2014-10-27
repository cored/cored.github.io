---
layout: post
title: "A kata a day: Spin words"
date: 2014-08-14 16:05
comments: true
categories: ["CodingKatas", "Ruby"]
---

# Problem Statement 

Write a function that takes in a string of one or more words, and returns the same 
string, but with all five or more letter words reversed (Just like the name of this Kata). 
Strings passed in will consist of only letters and spaces. Spaces will be included 
only when more than one word is present.

# Specs 

```ruby
describe SpinWords do 
  it 'returns same word for size less than five' do 
    expect("This".extend(SpinWords).spin).to eql "This"
  end

  it 'returns the reverse word for size greater or equal to five' do 
    expect("words".extend(SpinWords).spin).to eql "words".reverse
  end

  context 'phrases' do 
    it 'returns the same words for phrases smaller than 5' do 
      expect("This word".extend(SpinWords).spin).to eql "This word"
    end

    it 'returns the same the proper spin for "Hey fellow warriors"' do 
      expect("Hey fellow warriors".extend(SpinWords).spin).to eql "Hey wollef sroirraw"
    end
  end
end
```

# Implementation 

```ruby
module SpinWords 
  def spin 
    split(/\s/).map { |word|  word.size >= 5 ? word.reverse : word }.join(' ')
  end
end
```

# Bonus implementation 

```ruby
module SpinWords 
  def spin 
    gsub(/\w{5,}+/) { |w| w.reverse }
  end
end
```

# What did I learn from this kata?

Actually most of the implementation details from this solution I knew it
already; but what I think was worth while was starting for the most simple
problem that I had and keep iterating at first some of the first part of this
implementation did not work for the more general parts of the problem but then
I just change the implementation and did work. One of the the main things
I think I learned was that probably I have to learn how to identify the best
simple problem to start with; because I had to change the first implementation
entirely when I confront myself with more complex cases. So I think that's
worth researching on. 

Regarding the two implementations I think the gsub one is more clear than the
first one I did; also probably faster but I did not do any benchmark so I can't
know for sure. Anyways I think this was a fun kata for today; until next time.
