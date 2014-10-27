---
layout: post
title: "Another refactoring story"
date: 2014-02-24 16:30
comments: true
categories: ["Ruby", "Refactoring"]
---

TLDR; Keeping with the refactoring stories started with [My GPA in Code Climate is 3.59: A Refactoring Story](http://cored.github.io/blog/2014/02/19/my-gpa-at-code-climate-is-3-dot-59-a-refactoring-story/) I decided today to get a piece of code as much in shape as my knowledge let me, of course I'm always waiting for suggestions on how to improve so please do leave comments. 

This code base is part of an exercise from the [thoughtbot](http://www.thoughtbot.com) learning program [Learn Prime](http://learn.thoughtbot.com) which have great content go ahead and check it out you won't regret it. So getting that out of the way, let's see the code:

```ruby
class SurveyInviter
     EMAIL_REGEX = /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/

     def initialize(attributes = {})
          @survey = attributes[:survey]
          @message = attributes[:message] || ''
          @recipients = attributes[:recipients] || ''
          @sender = attributes[:sender]
     end

     attr_reader :message, :recipients, :survey

     def valid?
          valid_message? && valid_recipients?
     end

     def deliver
          recipient_list.each do |email|
               invitation = Invitation.create(
                    survey: @survey,
                    sender: @sender,
                    recipient_email: email,
                    status: 'pending'
               )
               Mailer.invitation_notification(invitation, @message)
          end
     end

     def invalid_recipients
          @invalid_recipients ||= recipient_list.map do |item|
               unless item.match(EMAIL_REGEX)
                    item
               end
          end.compact
     end

private

     def valid_message?
          @message.present?
     end

     def valid_recipients?
          invalid_recipients.empty?
     end

     def recipient_list
          @recipient_list ||= @recipients.gsub(/\s+/, '').split(/[\n,;]+/)
     end
end
```
In the other article regarding refactoring I used [Flog](http://github.com/seattlerb/flog) which is quite handy so let's use it with this one too:

```ruby
27.3: flog total
3.4: flog/method average

6.8: SurveyInviter#initialize lib/survey_inviter.rb:17
5.6: SurveyInviter#invalid_recipients lib/survey_inviter.rb:42
4.9: SurveyInviter#deliver lib/survey_inviter.rb:30


```
Surprise; that looks like a pretty solid report. Following my own rule regarding 10 or less on flog which is good. Which means that I'm done, right? Of course not this is a refactoring post so I have to at least rename a variable. Let's use another tool and see what it says this time I'm going to use [Reek](http://github.com/troessner/reek).

```ruby
❯ reek lib/survey_inviter.rb 
❯

```
Surprise again; so there's no warnings from reek regarding any code smell. So I think now I'm done, right? Well again I need to do something with this code. But I will stop just using tools I will just create some constraints for myself and try to shape the code on that; the constraints are basically this list: [Object Calisthenics](http://www.cs.helsinki.fi/u/luontola/tdd-2009/ext/ObjectCalisthenics.pdf)

## Only One Level Of Indentation Per Method

```ruby
class SurveyInviter
     EMAIL_REGEX = /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/

     def initialize(attributes = {})
          @survey = attributes[:survey]
          @message = attributes[:message] || ''
          @recipients = attributes[:recipients] || ''
          @sender = attributes[:sender]
     end

     attr_reader :message, :recipients, :survey

     def valid?
          valid_message? && valid_recipients?
     end

     def deliver
          recipient_list.each do |email|
               Mailer.invitation_notification(invitation(email), @message)
          end
     end

     def invalid_recipients
          @invalid_recipients ||= recipient_list.map do |item|
               item unless item.match(EMAIL_REGEX)
          end.compact
     end

     private

     def invitation(email)
          Invitation.create(
               survey: @survey,
               sender: @sender,
               recipient_email: email,
               status: 'pending'
          )
     end

     def valid_message?
          @message.present?
     end

     def valid_recipients?
          invalid_recipients.empty?
     end

     def recipient_list
          @recipient_list ||= @recipients.gsub(/\s+/, '').split(/[\n,;]+/)
     end
end
```

## Don't Use The ELSE Keyword

This code doesn't have else keyword so we can keep on without changing it.

## Wrap All Primitives And Strings and First Class Collections

So even when we have the notion of _Everything is an Object_ in Ruby still I would count types like [Fixnum](http://www.ruby-doc.org/core-2.1.0/Fixnum.html), [String](http://www.ruby-doc.org/core-2.1.0/String.html) and [Array](http://www.ruby-doc.org/core-2.1.0/Array.html) as primitives for the following changes.

```ruby
class SurveyInviter

     def initialize(attributes = {})
          @survey = attributes[:survey]
          @message = Message.new(attributes[:message])
          @recipients = Recipients.new(attributes[:recipients])
          @sender = attributes[:sender]
     end

     attr_reader :message, :recipients, :survey
     def valid?
          message.valid? && recipients.valid?
     end

     def deliver
          recipients.each do |recipient|
               Mailer.invitation_notification(invitation(recipient), message.to_s)
          end
     end

     private
     def invitation(email)
          Invitation.create(
               survey: @survey,
               sender: @sender,
               recipient_email: email,
               status: 'pending'
          )
     end
end

class Message
     def initialize(message)
          @message = message || ''
          freeze
     end

     def valid?
          @message.present?
     end

     def to_s
          @message
     end
end

class Recipients
     EMAIL_REGEX = /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/

     def initialize(recipients)
          @recipients = build_recipient_list(recipients || '')
          freeze
     end

     def valid?
          invalid_recipient_list.empty?
     end

     def each
          return enum_for(:each) unless block_given?

          @recipients.each do |recipient|
               yield recipient
          end
     end

     private
     def build_recipient_list(recipients)
          recipients.gsub(/\s+/, '').split(/[\n,;]+/)
     end

     def invalid_recipient_list
          @recipients.map do |item|
               item unless item.match(EMAIL_REGEX)
          end.compact
     end
end
```


## One Dot Per Line

I just found one method which more than one dot in the same line and that's the only change in here so I will just show that particular class. 



```ruby

class Recipients
  EMAIL_REGEX = /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/

  def initialize(recipients)
    @recipients = build_recipient_list(recipients || '')
    freeze
  end

  def valid?
    invalid_recipient_list.empty?
  end

  def each 
    return enum_for(:each) unless block_given? 

    @recipients.each do |recipient|
      yield recipient
    end
  end

  private 
  def build_recipient_list(recipients)
    remove_space(recipients).split(/[\n,;]+/)
  end

  def remove_space(recipients)
    recipients.gsub(/\s+/,'')
  end

  def invalid_recipient_list
    @recipients.map do |item|
      item unless item.match(EMAIL_REGEX)
    end.compact
  end
end
```

## Don't Abbreviate

I don't have any abbreviations at the moment on this particular piece of code and most name make sense to me. Probably I'm wrong but that's my perception at the moment. Btw this constraint mean not to shrink method names, variables and constants. 

## Keep All Entities Small

So the rule says that no class should contain more than 50 lines and no package should contain more than 100 files. Both things are taking care of now; the only thing that I did at the end was to move the classes to it's own files. 

## No Classes With More Than Two Instance Variables

This was kinda hard; but it just points me to the fact that I did not follow rule number 3 properly; because not every primitive is wrapped. The advantage of this rule is that force you to encapsulate and distinguish classes that do orchestration which does that work as containers. Which reminds me the distintion in the Book [Growing Object Oriented Design Guided by Tests](http://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627) between objects and values. Of course the number of instance variables is arbitrary but I think is a good number that help me stretch my brain a little bit ;-)

```ruby

class SurveyInviter
 
     def initialize(attributes = {})
          @survey_manager = SurveyManager.new(
               Survey.new(attributes[:survey]),
               Sender.new(attributes[:sender])
          )
          @message_manager = MessageManager.new(
               Message.new(attributes[:message]),
               Recipients.new(attributes[:recipients])
          )
     end
 
     def valid?
          message_manager.is_message_valid? && message_manager.are_recipients_valid?
     end
 
     def deliver
          message_manager.all_recipients do |recipient|
               Mailer.invitation_notification(invitation(recipient), message_manager.message)
          end
     end
 
     private
     attr_reader :message, :recipients, :survey, :message_manager
 
     def invitation(email)
          Invitation.create(
               survey: @survey_manager.survey,
               sender: @survey_manager.sender,
               recipient_email: email,
               status: 'pending'
          )
          end
     end
end

class MessageManager 

  def initialize(message, recipients) 
    @message = message 
    @recipients = recipients
    freeze
  end

  def all_recipients(&block)
    @recipients.each(&block)
  end

  def is_message_valid?
    @message.valid?
  end

  def are_recipients_valid?
    @recipients.valid?
  end

  def message
    @message.to_s
  end

end

class SurveyManager 
  def initialize(survey, sender)
    @survey = survey 
    @sender = sender
    freeze
  end

  def survey 
    @survey.to_s
  end

  def sender
    @sender.to_s  
  end
end
```

## No Getters/Setters/Properties

This particular rule in fact refers to [Tell Don't Ask](http://pragprog.com/articles/tell-dont-ask) which means that is fine to use accessors to know an object's inside state but not to make any decisions with that data outside the object. So basically I just remove the attr_accessors that I had in the ```SurveyInviter``` class just because I don't need them any more and that will expose data that I really don't need to use outside of the class. 



## Conclusion 

So there you have it; another happy ending for a refactoring story; I learned a lot from this one, hope I shared as much. Enjoy.

