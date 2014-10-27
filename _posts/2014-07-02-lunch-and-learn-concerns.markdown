---
layout: post
title: "Lunch and Learn: Lunch and Learn: Concerns, Decorators, Presenters,
Service Objects, Helpers, Help Me Decide! by Justin Gordon"
date: 2014-07-02 11:32
comments: true
categories: ["Lunch-n-Learn", "Ruby", "Rails", "Design Patterns"]
---

<iframe width="560" height="315" src="//www.youtube.com/embed/bHpVdOzrvkE?list=PLE7tQUdRKcyZ5jfnbS_osIoWzK_FrwKz5" frameborder="0" allowfullscreen></iframe>

## What is this talk about?

* How to trim the fat of controllers and models 
* 5 > lines 
* 100 > classes 
* One instance variable per view
* DRY
* Easy to test 
* Clarity 
* Easy to Find
* Easy to change
* Concerns
* Decorators 
* Presenters 
* Split up controllers 
* Move logic to Models 
* Validation classes
* How to use concerns 
* Discover a set of related code for a problem domain 
* Create a module with extends ActiveSupport::Concern
* Move code into the concern 
* Break out tests into corresponding files for the concern
* How to use draper decorators 
* Use it when the model has presentation logic
* How to use presenters
* Creates a PORO that wraps up the values and logic going from the controller to the view
* Moving logic to models 
* Let the logic inside the models using as much features from Rails as possible or introduce a service objects that do the iteration between two or more models
* Use Rails models, validations and controller for their proper jobs 
* KISS
* Don't invent patterns that don't need to be invented 
* Know the Rails way 

## What do I think about this talk?

Justin is quite enjoyable presenter but I disagree with him in some points. The main thing that Sandi's rules try to accomplish is not to break the SRP on classes using concerns do the opposite of this because the public API of those models still expose a lot of stuffs the only thing that's happening is that the logic is in another place. Another thing that I dislike about the talk in general is that is repeating the same thing that most of the people talking about this pattern is talking about but is not solving the main issue when is fine to use this approaches? At the end of the talk he just says basically drops most of the already and well known OOP patterns out there and use Rails effectively; I agree that it best to use what you have at first but we as software developers aim to have easier to change systems and when you have a controller action doing a lot of stuffs in it and also model which a lot of lines of code implying the same that right there is just harder to change in my book. The last part that I really don't got very well from his talk with the distinction between presenters and decorators it's seems to me that both things overlaps in a Rails application because they hide view logic in it's internals so when to use one or the other again?

At this point I still have mixed feelings between knowing when to use those patterns and when not, my experience says that more often than not you still need to find ways to better design your systems for the sake of yourself and your teammates. The talk is ok for people that are not familiar with the terms but it can put the viewer in the same position as he started asking for help on when to use those patterns better worst it even can make the viewer just to stick to Rails way every time he/she start a new project.

