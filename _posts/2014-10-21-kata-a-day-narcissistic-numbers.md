---
layout: post
title: "Kata a day: Narcissistic numbers"
comments: true
categories: ["CodingKata", "Ruby"]
---

# Problem Statement

A [Narcissistic Number](https://en.wikipedia.org/wiki/Narcissistic_number) is a
number which is the sum of its own digits, each raised to the power of the
number of digits.

# Specification

```ruby
describe Narcissistic do
  it 'returns true for one' do
    Narcissistic.(1).must_equal true
  end

  it 'returns false for for 22' do
    Narcissistic.(22).must_equal false
  end

  it 'returns true for 153' do
    Narcissistic.(153).must_equal true
  end

  it 'returns true for 1634' do
    Narcissistic.(1634).must_equal true
  end
end
```

# Implementation

```ruby
module Narcissistic
  extend self

  def call(num)
    numbers = num.to_s.chars
    numbers.map(&:to_i).
      inject(0) { |sum, number| sum += number**numbers.size } == num
  end
end
```

# What did I learn about this kata?

For starters; I think that one of the things that I learned was related to idiomatic
Ruby instead of anything else; it is obvious that this problem is very simple. So
What ocurrs to me was just to switch things up a little bit not from the complexity
stand point just from the idiomatic stand point.

As Ruby have the same notion as Perl does *TMTOWTDI* I decided to just switch some
small details of the implementation. For instance:

```ruby
  numbers.map { |n| n.to_i**numbers.size }.inject(:+)
```

I feel this looks more concise at least to me; actually have to give it a little bit
more of thought; anyways that was the Kata for today. If you want to try to do the
same and find problems to solve on a daily or weekly basics just go ahead to
(Codewars)[http://codewars.com] great resource for keep getting better at
programming. Happy hacking!

