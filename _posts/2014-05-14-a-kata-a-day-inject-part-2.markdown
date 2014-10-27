---
layout: post
title: "A Kata a day: Inject part 2"
date: 2014-05-14 11:02
comments: true
categories: ["CodingKata", "Ruby"]
---

This is the continuation of a kata that I did not finish in the hour that
I normally employ on practicing with katas. So here it goes: 

First I will show you the specs that needs to be implemented: 

```ruby
  context '#nested_key' do
    it 'finds the nested key when present' do
      data = { outer: { inner: 'value' } }
      expect(nested_key([:outer, :inner], data)).to eq('value')
    end

    it 'returns nil when missing a level' do
      data = { other: 'value' }
      expect(nested_key([:outer, :inner], data)).to be_nil
    end
  end
```

Looks pretty straightforward let me write down the implementation

```ruby
def nested_keys(keys, hash)
  keys.inject(hash) do |value, key| 
    value = value[key] if value
  end 
end
```

There's something interesting about this implementation in my honest opinion
and is that it doesn't show intent; I won't go ahead and refactor this piece of
code but I will think about this for a while. Is inject better most of the time
or not?

The next problem actually was a rewrite to use inject instead of the following: 

```ruby
 def self.search(query)
   # TODO: rewrite to use inject instead of each and mutation
   relation = self
   query.split(' ').each do |keyword|
     relation = relation.where('body LIKE ?', "%#{keyword}%")
   end
   relation
 end
```

And here are the specs:

```ruby
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
```

My rewrite

```ruby
def self.search(query)
  query.split(' ').inject(self) do |relation, keyword|
    relation = relation.where('body LIKE ?', "%#{keyword}%")
  end
end
```

Last spec: 

```ruby
  context Category, '#find_by_path' do
    it 'finds a top-level category' do
     Category.create!(name: 'Findme')

      expect(Category.find_by_path('Findme').try(:name)).to eq('Findme')
    end

    it 'finds a nested category' do
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
```

Implementation: 

```ruby
 def self.find_by_path(path)
   path.split('/').inject(self) do |category, path|
     category.children.find_by_name(path)
   end
 end
```

The last one was a little bit hard to be honest; I spend a lot of time thinking
about how to solve it and even this was some sort of epiphany based on reading
somebody else code; so that is that. 

## What I learned from this kata?
* I need to spend more time reading the documentation of the language in
question. 
