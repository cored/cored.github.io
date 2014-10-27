---
layout: post
title: "A kata a day: Hours"
date: 2014-04-21 10:32
comments: true
categories: ["Software Craftmanship", "CodingKatas"]
---

This will be a series regarding what the title implies; I will do
a coding kata each day for an hour. It doesn't matter if I finish the kata or
not, if I get bored of it I will switch it; why you may ask? Well I don't think
that repeating the same thing each time will lead me to anything than improving
at the same type of problems I want to expand my horizons at programming, that's why.
So let's start with what I did today. 

This problem name is **Hours** I think the first time I saw it was in an interview
time I saw it; which by the way I perform poorly probaby because of the lack of
practice at the time. I figure that it would be a good problem to practice with. 
I don't have the problem statement but I do have a set of specs so here it goes: 

```ruby
describe Restrictions do 
  let(:restrictions) { Restrictions.new }

  it 'pretty print out the days and hours' do 
    restrictions << Restriction.new("Monday", "3:00", "4:00")
    restrictions << Restriction.new("Tuesday", "4:00", "5:00")

    restrictions.print_pretty.should == ["Monday 3:00 - 4:00", "Tuesday 4:00 - 5:00"]
  end

  pending 'group days with similar hours' do 
    restrictions << Restriction.new("Monday", "3:00", "4:00")
    restrictions << Restriction.new("Tuesday", "4:00", "5:00")
    restrictions << Restriction.new("Wednesday", "4:00", "5:00")

    restrictions.print_pretty.should == ["Monday 3:00 - 4:00", "Tuesday, Wednesday 4:00 - 5:00"]
  end
end
```
Here is the simplest code that make the first spec to pass:

```ruby
class Restrictions
  def initialize 
    @restrictions = []
  end

  def <<(restriction)
    @restrictions << restriction
  end

  def print_pretty 
    @restrictions.map do |restriction| 
      "#{restriction.day} #{restriction.initial_time} - #{restriction.end_time}"
    end
  end

end

class Restriction

  attr_reader :day, :initial_time, :end_time
  def initialize(day, initial_time, end_time)
    @day = day 
    @initial_time = initial_time 
    @end_time = end_time
  end

end
```
Pretty straighforward, so following with the red, green, refactor cycle
I decided to do an simple extraction because I thought that each
``Restriction`` should know how to display it's own data, so I did the
following change.

```ruby
class Restrictions
  def initialize 
    @restrictions = []
  end

  def <<(restriction)
    @restrictions << restriction
  end

  def print_pretty 
    @restrictions.map(&:to_s)
  end

end

class Restriction

  attr_reader :day, :initial_time, :end_time
  def initialize(day, initial_time, end_time)
    @day = day 
    @initial_time = initial_time 
    @end_time = end_time
  end

  def to_s
    "#{day} #{initial_time} - #{end_time}"
  end

end
```
I felt that this looks pretty solid; so I went ahead to implement the next
failing spec: 

```ruby
class Restrictions
  def initialize 
    @restrictions = []
  end

  def <<(restriction)
    @restrictions << restriction
  end

  def print_pretty 
    @restrictions.group_by do |restriction|
      "#{restriction.initial_time} - #{restriction.end_time}"
    end.map do |time_range, restrictions|
      "#{restrictions.map { |restriction| restriction.day }.join(', ')} #{time_range}"
    end
  end

end

class Restriction

  attr_reader :day, :initial_time, :end_time
  def initialize(day, initial_time, end_time)
    @day = day 
    @initial_time = initial_time 
    @end_time = end_time
  end

  def to_s
    "#{day} #{initial_time} - #{end_time}"
  end

end
```
So this implementation make the spec to pass; now is time for some refactoring:

```ruby
class Restrictions
  def initialize 
    @restrictions = []
  end

  def <<(restriction)
    @restrictions << restriction
  end

  def print_pretty 
    @restrictions.group_by(&:time_range).map do |time_range, restrictions|
      "#{restriction_joined_by_day(restrictions)} #{time_range}"
    end
  end

  private
  def restriction_joined_by_day(restrictions)
    restrictions.map { |restriction| restriction.day }.join(', ')
  end

end

class Restriction

  attr_reader :day, :initial_time, :end_time
  def initialize(day, initial_time, end_time)
    @day = day 
    @initial_time = initial_time 
    @end_time = end_time
  end

  def time_range
    "#{initial_time} - #{end_time}"
  end

end
```

The refactor is also pretty simple; I just remove the ```#to_s``` method from
```Restriction``` because I wasn't using it but the new method is just
a modification of that one; which leads me to a simplification of the
grouping of the restrictions; also I just extracted the loop implementation to
another method inside the ```Restrictions``` colllection; why? I just did it
because I felt that the new name show the intention of that inner loop. 

## What did I learn from this kata?
* When the output of an iteration is an array transformation
probably the most useful method will be ```#map```.
* If you don't have any particular use for a method in a given situation just
erase the code. 
* ```#group_by``` accept strings interpolation in the block
* I knew this one; but is a good reminder; always extract method to show
intention.  

## Conclusion 
This was a great exercise and I learned some good stuffs that I did not know
before; I'll wait for tomorrow on the new kata and see what that brings me;
meanwhile I'm hoping suggestions on the comments on this problem or in other
ways to improving at programming; until next time. 

## Resources
[Enumerable#map](http://www.ruby-doc.org/core-2.1.1/Enumerable.html#method-i-map)
