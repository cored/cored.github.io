---
layout: post
title: "Service Objects in Rails"
date: 2015-02-26T11:07:01-04:00
---

TLDR; 

*Service Layer to represent a domain-oriented layer of behaviors that provide
an API for the domain layer.* - Martin Fowler

There's been a lot of talk about service objects in Rails; [Code Climate](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/), [Hexagonal Rails](https://www.agileplannerapp.com/blog/building-agile-planner/refactoring-with-hexagonal-rails) and many others places.
This post goal is to try to centralize as much info as I can from the research
that I've been making with this type of approach in Rails applications.

Some people say there are different flavors of service objects; what I normally
suggest is that you need to follow the principles as much as possible. For
instance following [SRP](http://en.wikipedia.org/wiki/Single_responsibility_principle) will lead you to a proper separation of concerns between
your business logic and the framework around it.

The following are the different approaches that people tend to use when
implementing service objects in Rails:

I will use one action from a controller within the project [Hours](https://github.com/DefactoSoftware/Hours).

```ruby
class CategoriesController < ApplicatoinController
  def create
    @category = Category.new(category_params)
    if @category.save
      redirect_to categories_path, notice: t(:category_created)
    else
      @categories = Category.by_name
      render "categories/index"
    end
  end
end
```

## Refactoring: Some people implement service objects like this

```ruby
class CategoryService
  attr_reader :categories, :category

  def call(category_params)
    @category = Category.new(category_params)
    if @category.save
      true
    else
      @categories = Category.by_name
      false
    end
  end
end

class CategoriesController < ApplicationController
  # ...
  def create
    category_service = CategoryService.new
    if category_service.call(category_params)
     @category = category_service.category
     redirect_to categories_path, notice: t(:category_created)
    else
     @categories = category_service.categories
     @category = category_service.category
     render "categories/index"
    end
  end
  # ...
end
```

# Advantages

- Test in isolation
- The controller doesn't interact directly with persistence logic

# Disadvantages

- Coupling between the controller and the service
- The controller still makes decisions based on the service state
- Breaks "Tell, don't ask"
- We are making decisions in two different places

## Refactoring: Matt Wynne - Hexagonal Rails way

```ruby
class CategoryService < Struct.new(:listener)
  def call(params)
   category = Category.new(params)
   if category.save
    listener.create_on_success(category)
   else
    listener.create_on_failure(category,
    Category.by_name)
   end
  end
end

class CategoriesController < ApplicationController
  def create
   @category = CategoryService.new(self).call(category_params)
  end

  def create_on_success(category)
   @category = category
   redirect_to categories_path, notice: t(:category_created)
  end

  def create_on_failure(category, categories)
   @category = category
   @categories = categories
   render "categories/index"
  end
end
```

# Advantages

- There is no more decision making in the controller based on the service state
- Reduce coupling
- The business logic is now part of a well define object
- Proper encapsulation of data inside the service.

# Disadvantages

- Most controllers have more than one action in it; so you will need to write
  two new public methods for each action.

### Side note

You could always use [one action per conroller](https://github.com/jonleighton/focused_controller).

## Refactoring: Using lambdas instead of explicit method definitions

```ruby
class CategoryService
  def initialize(params)
   @category_params = params
  end

  def call(success:, failure:)
   category = Category.new(params)
   if category.save
    success.call(category)
   else
    failure.call(category, Category.by_name)
   end
  end

  private

  attr_reader :category_params
end

class CategoriesController < ApplicationController
  def create
   CategoryService.new(category_params).call(
   success: -> do |category|
    @category = category
    redirect_to categories_path, notice: t(:category_created)
   end,
   failure: -> do |category, categories|
    @category = category
    @categories = categories
    render "categories/index"
   end
   )
  end
end
```

# Advantages

- Every single advantage of the prior refactorings
- Do not pollute the public API of the controller

# Disadvantages

- Is not that good for code that runs independently of whatever it's happening
  at the moment of action execution; eg. logging; analytics.

## Refactoring: Using the wisper gem for service definition

```ruby
class CategoryService
  include Wisper::Publisher

  def call(params)
   category = Category.new(params)
   if category.save
    broadcast :category_created_successfully, category
   else
    broadcast :category_created_unsuccessfully,
    category, Category.by_name
   end
  end
end

class CategoriesController < ApplicationController
  def create
   category_service = CategoryService.new

   category_service.on :category_created_successfully do |category|
    @category = category
    redirect_to categories_path, notice: t(:category_created)
   end

   category_service.on :category_created_unsuccessfully do |category, categories|
    @category = category
    @categories = categories
    render "categories/index"
   end

   category_service.call(category_params)
  end
end
```

# Advantages

- Every single advantage of the other refactorings
- Easier to compose orthogonal behavior; for instance:

```ruby
class CategoriesController < ApplicationController
  def create
   category_service = CategoryService.new
   category_service.subscribe(LoggingListener.new)

   category_service.on :category_created_successfully do |category|
    @category = category
    redirect_to categories_path, notice: t(:category_created)
   end

   category_service.on :category_created_unsuccessfully do |category, categories|
    @category = category
    @categories = categories
    render "categories/index"
   end

   category_service.call(category_params)
  end
end

class CategoryService
  include Wisper::Publisher

  def call(params)
   category = Category.new(params)
   broadcast :log, self
   if category.save
    broadcast :category_created_successfully, category
   else
    broadcast :category_created_unsuccessfully,
    category, Category.by_name
   end
  end
end

class LoggingListener
 def log(entity)
  puts "Info: #{entity.inspect}"
 end
end
```

- Adheres to the [Open/Closed principle](http://en.wikipedia.org/wiki/Open/closed_principle)

# Disadvantages

- Team adoption

## Summary

At first sight people don't see the benefits of extracting service objects in
their Rails application and tend to think this is over engineering but having
proper place for individual functionality help us with the maintainability and
extensibility of the code. When you have several smaller objects in a system
it's easier to resonate about those smaller pieces than bigger ones.

## Resources 

- [Hexagonal Rails](https://www.youtube.com/watch?v=CGN4RFkhH2M)
- [Decoupling from Rails](https://www.youtube.com/watch?v=tg5RFeSfBM4)
- [Domain Driven Rails](http://www.windycityrails.org/videos/2014/#6)
