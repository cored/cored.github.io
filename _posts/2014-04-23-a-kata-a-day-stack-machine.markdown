---
layout: post
title: "A kata a day: Stack Machine"
date: 2014-04-23 21:41
comments: true
categories: ["CodingKata", "Ruby", "Software Craftmanship"]
---

Today's kata was an stack machine; I took the exercise from another interview;
so here goes the problem statement: 

```
A stack machine is given. It contains an internal stack that can store arbitrary number of 12-bit unsigned integers. The machine processes a string of characters in the following way:
characters of the string are processed one by one;
if the current character is a digit ('0'-'9'), the machine pushes the value of that digit onto its internal stack;
if the current character is '+', the machine pops two topmost values from its internal stack, adds them and pushes the result onto the stack;
if the current character is '*', the machine pops two topmost values from its internal stack, multiplies them and pushes the result onto the stack;
after the machine has processed the whole string it returns the topmost value of its internal stack as the result;
the machine reports an error if any operation it performed (addition or multiplication) resulted in an overflow or underflow;
the machine reports an error if it tried to pop an element from its internal stack and the stack was empty.
```

So here it goes; first I'll show you the specs: 

```ruby
describe StackMachine do 
  it 'should push 1 into the stack' do 
    StackMachine.new('1').emulate.should == 1 
  end

  it 'should push 1,3 into the stack' do 
    stack_machine = StackMachine.new('13')
    stack_machine.emulate
    stack_machine.stack.should == [1,3]
  end

  it 'should perform addition with 13+' do 
    StackMachine.new('13+').emulate.should == 4
  end

  it 'should return [4, 6, 2] for 13+62' do 
    stack_machine = StackMachine.new('13+62')
    stack_machine.emulate
    stack_machine.stack.should == [4,6,2]
  end

  it 'should perform multiplication' do 
    StackMachine.new('13+62*').emulate.should == 12
  end

  context 'edge cases' do 
    it 'should return 76 for 13+62*7+*' do 
      StackMachine.new('13+62*7+*').emulate.should == 76
    end

    it 'should return -1' do 
      StackMachine.new('11++').emulate.should == -1
    end
  end
end
```

Now the implementation that solve this:

```ruby
class StackMachine 
  attr_reader :stack
  def initialize(values)
    @values = values 
    @stack = []
  end

  def emulate 
    @values.scan(/\d|\+|\*/).each do |value|
      @stack << value.to_i if value =~ /\d/
      if value =~ /\+|\*/
        if @stack.length > 1
          @stack << @stack.pop.send(value.to_sym, @stack.pop) 
        else 
          return -1 
        end
      end
    end
    @stack.last
  end
end
```

I know this looks ugly even for my standards; so let me try to refactor this
a little bit: 

```ruby
class StackMachine 
  attr_reader :stack
  def initialize(values)
    @values = values 
    @stack = []
  end

  def emulate 
    @values.scan(/\d|\+|\*/).each do |value|
      add_value_to_stack_if_digit(value)    
      if is_an_arithmetic_character?(value) 
        if @stack.length > 1
          @stack << @stack.pop.send(value.to_sym, @stack.pop) 
        else 
          return -1 
        end
      end
    end
    @stack.last
  end

  private 
  def add_value_to_stack_if_digit(value)
    @stack << value.to_i if value =~ /\d/
  end

  def is_an_arithmetic_character?(value)
    value  =~ /\+|\*/
  end
end
```

This got a little bit better; but code can always be improve so let me try
again: 

```ruby
class Stack 
  attr_reader :stack

  def initialize
    @stack = []
    @has_error = false
  end

  def add(value)
    @stack << value.to_i if value =~ /\d/
  end

  def perform_operation(value)
    if is_an_arithmetic_character?(value) 
      if @stack.length > 1
        @stack << @stack.pop.send(value.to_sym, @stack.pop) 
      else 
        @has_error = true       
      end
    end
  end

  def has_error?
    @has_error
  end

  def last 
    @stack.last
  end

  private 
  def is_an_arithmetic_character?(value)
    value  =~ /\+|\*/
  end
end

class StackMachine 
  def initialize(values)
    @values = values 
    @stack = Stack.new
  end

  def emulate 
    @values.scan(/\d|\+|\*/).each do |value|
      @stack.add(value)    
      @stack.perform_operation(value)
      return -1 if @stack.has_error?
    end
    @stack.last
  end

  def stack 
    @stack.stack
  end
end
```
I think this looks better.

## What I've learned from this Kata?
* ``String#scan`` yes I used this method before but is always good to go back
  to the documentation and recheck what we think we know. 
* Private methods keeps telling me that there's probably and object hiding
  somewhere

## Conclusions
I could go further and extract another object probably two more; ``Values`` and
``Value`` comes to my mind; but at this time I just stop here maybe next month
I come back to this problem and found a better and more cleaner solution for
now I'm satisfied.
