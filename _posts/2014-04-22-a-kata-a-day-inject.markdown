---
layout: post
title: "A kata a day: inject; part 1"
date: 2014-04-22 09:20
comments: true
categories: ["Software Craftmanship", "CodingKatas"]
---

Continue with the coding katas series; I will today work on a problem which is
part of the [Learn Prime](http://learn.thoughtbot.com) program, which has a lot
of good resources to keep growing as a software developer in general. Geetting
that little with no profit ad out of the way let's see what's the problem
entails. 

Extracted from the project's README: 
```
Implement the methods in lib/inject.rb marked TODO using inject.

Some methods are implemented already, but not using inject. First, rewrite
those methods to use inject, while keeping the tests in spec/inject_spec.rb
passing.

Next, delete the pending lines in spec/inject_spec.rb one-by-one and make
them pass by implementing the corresponding methods using inject.
```

Here is the entire spec for this problem: 

```ruby 
require 'rspec'
require 'active_record'
require 'benchmark'

PROJECT_ROOT = File.expand_path('../..', __FILE__)

Dir.glob(File.join(PROJECT_ROOT, 'lib', '*.rb')).each do |file|
  require(file)
end

ActiveRecord::Base.establish_connection(
  adapter:  'sqlite3',
  database: File.join(PROJECT_ROOT, 'test.db')
)

class CreateSchema < ActiveRecord::Migration
  def self.up
    create_table :categories, force: true do |table|
      table.string :name
      table.string :body
      table.integer :parent_id
    end
  end
end

CreateSchema.suppress_messages { CreateSchema.migrate(:up) }

describe 'inject exercise' do
  context '#all_equal?' do
    it 'returns true if all elements are equal to the argument' do
      expect(all_equal?(1, [1, 1, 1])).to be_true
    end

    it 'returns false if any element is unequal to the argument' do
      expect(all_equal?(1, [2, 1, 1])).to be_false
      expect(all_equal?(1, [1, 2, 1])).to be_false
      expect(all_equal?(1, [1, 1, 2])).to be_false
    end
  end

context '#count_equal' do
  it 'counts the number of elements equal to the argument' do
    expect(count_equal(1, [1, 2, 3, 1, 2, 3])).to eq(2)
  end

  it 'returns zero for an empty array' do
    expect(count_equal(1, [])).to eq(0)
  end
end

context '#nested_key' do
  it 'finds the nested key when present' do
    pending
    data = { outer: { inner: 'value' } }
    expect(nested_key([:outer, :inner], data)).to eq('value')
  end

  it 'returns nil when missing a level' do
    pending
    data = { other: 'value' }
    expect(nested_key([:outer, :inner], data)).to be_nil
  end
end

context Category, '#search' do
  it 'finds entries by keyword search' do
    Category.create! name: 'one', body: 'keyword1 other words'
    Category.create! name: 'two', body: 'keyword2'
    Category.create! name: 'three', body: 'keyword1 keyword2 other words'
    Category.create! name: 'four', body: 'keyword1 keyword2 other words'

result = Category.search('keyword1 keyword2').map(&:name)

     expect(result).to match_array(%w(three four))
   end
 end

 context Category, '#find_by_path' do
   it 'finds a top-level category' do
     pending
     Category.create!(name: 'Findme')

     expect(Category.find_by_path('Findme').try(:name)).to eq('Findme')
   end

   it 'finds a nested category' do
     pending
     root = Category.create!(name: 'Root')
     child = Category.create!(name: 'Child', parent: root)
     Category.create!(name: 'Grandchild', body: 'Orphan')
     Category.create!(name: 'Grandchild', parent: child, body: 'Expected')
     Category.create!(name: 'Grandchild', body: 'Orphan')

     result = Category.find_by_path('Root/Child/Grandchild').try(:body)

     expect(result).to eq('Expected')
   end

   it 'returns nil when missing a level' do
     Category.create!(name: 'Root')

     result = Category.find_by_path('Root/Child/Grandchild')

     expect(result).to be_nil
   end
 end

after { Category.delete_all }
end

```

As the problem statement says I started with the TODO's

```ruby
def all_equal?(argument, elements)
  elements.inject(true) do |all_equals, element| 
    all_equals = element == argument && all_equals
  end
end
```

Pretty straithtforward. 

```ruby
# Return the number of elements that are equal to the argument.
def count_equal(argument, elements)
  # TODO: rewrite to use inject instead of select and size
  equal = elements.select do |element|
    element == argument
  end
  equal.size

  # Version with inject
  elements.inject(0) do |equal, element|
    equal += 1 if element == argument
    equal
  end
end
```

## What I learned from this kata 
* ``first, *rest =`` this can separate the head from the tail of a list 
