---
layout: post
title: "A kata a day: Friends"
date: 2014-04-25 18:35
comments: true
categories: ["CodingKata", "Software Craftmanship", "Ruby"]
---

This problem is more of the way I think about tackling a new project than the
way I actually do some refactor knowing what I'm going to do; that read
a little bit confusing, so I will try to explain myself. The problem is to
build a feature from the grown up but I have to do some assumptions of he end
result for it, which leads me to an over complicated implementation at first
which I won't show and to a more straitht forward one which is the one I will
show you guys; btw this was also part of another job interview. Here goes the
problem statement: 

```
Write an algorithm, which synchronizes two lists of friends. 
First list is stored locally as a JSON/ActiveRecord/Array of OpenStructs/Something else (your choice). 
The other list is a result of a remote API call (stubbed/mocked in specs as JSON).

The algorithm should:
1) Add friends not present locally (based on `id`)
2) Update names of friends existing locally (based on `id`)
3) Delete local friends that are not present in the remote response (based on `id`)

Exemplary API response:
{ "friends" : [ {"id":"1", "name":"jack"}, {"id":"2", "name":"john"} ] }
```

For this problem I had to implement my own set of spec and what I thought was
good ones, so here it goes: 

```ruby
class Service; end

describe Syncronizer do 
  context 'syncronize two lists' do 
    it 'add friends not present' do 
      Service.stub(:retrieve_friends) { '{ "friends" : [ {"id":"1", "name":"jack"}, {"id":"2", "name":"john"} ] }' }

      syncronizer = Syncronizer.new
      syncronizer.perform.first.name.should == 'jack'
    end

    it 'updates existing friends names based on id' do 
      Service.stub(:retrieve_friends) { '{ "friends" : [ {"id":"1", "name":"jack"}, {"id":"2", "name":"john"} ] }' }

      syncronizer = Syncronizer.new
      syncronizer.perform

      Service.stub(:retrieve_friends) { '{ "friends" : [ {"id":"1", "name":"bob"}, {"id":"2", "name":"john"} ] }' }

      friends = syncronizer.perform
      friends.first.name.should == 'bob'
      friends.size.should == 2
    end

    it 'delete local friends that are not present in the response' do 
      Service.stub(:retrieve_friends) { '{ "friends" : [ {"id":"1", "name":"jack"}, {"id":"2", "name":"john"} ] }' }

      syncronizer = Syncronizer.new
      syncronizer.perform

      Service.stub(:retrieve_friends) { '{ "friends" : [ {"id":"2", "name":"molly"} ] }' }

      friends = syncronizer.perform
      friends.first.name.should == 'molly'
      friends.size.should == 1
    end
  end
end
```

And here is the final code

```ruby
require 'json'

Friend = Struct.new(:id, :name)

class Friends
  def initialize 
    @friends = []
  end

  def add(friends)
    @friends += friends 
  end

  def update_friends_by_id
    resulted_friends = []
    @friends.group_by(&:id).map do |id, friends| 
      resulted_friends << replace_friend_by_id(friends)
    end.flatten
    @friends = resulted_friends.flatten
  end

  def remove_friends_not_in(friends)
    @friends.keep_if { |friend| any_friend_id_equal_to(friends, friend.id) }
  end

  private 
  def any_friend_id_equal_to(friends, id)
    friends.any? { |fr| fr.id == id } 
  end

  def replace_friend_by_id(friends)
    if friends.size > 1
      friends[0] = friends[1] 
      friends.delete_at 1
    else 
      friends
    end
  end
end

class Syncronizer
  def initialize
    @friends = Friends.new
  end

  def perform
    @friends.add friend_response
    @friends.update_friends_by_id
    @friends.remove_friends_not_in(friend_response)
  end

  def service 
    Service
  end

  private 
  def friend_response 
    parse_service_response
  end

  def parse_service_response 
    JSON.parse(service.retrieve_friends)["friends"].map do |friend|
      Friend.new(friend['id'], friend['name'])
    end
  end
end
```

Obviously I spend some time doing some refactoring to this code; but I just
went ahead and paste the final solution for the sake of breavity. 

# What did I learn from this Kata 
* Always to start with an skeleton or prototype: I did not know what the final
solution would be I even did not know how to start; I just went ahead and
create something that was similar to the expected solution in fact I did not
even test drive my first implementation I just touch the water.
* Stop thinking about design upfront: I spend a whole lot of time just thinking
about how to implement the entire thing without even having anything done, so
I think that's a good rule to have in mind.

# Resources
[Kickstart your next project with a Walking Skeleton](http://blog.codeclimate.com/blog/2014/03/20/kickstart-your-next-project-with-a-walking-skeleton)
