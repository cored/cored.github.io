---
layout: post
title: My List of Best Practices for Rails Applications
date: 2015-06-23T07:11:22-04:00
category: ["Rails", "Best Practices"]
---

Today I decided to write about a list of rules that I follow when working with
Rails. This rules are by no mean the way to go for everyone take what you think
is correct and try to use it. I will try to update this list as new rules
appears on my experiment.

## Controllers
- Do not call any query methods directly to a model (Finders methods are ok to
  call).
- Do not catch active record exceptions.
- Do not use if statements inside actions (Use service or interactor objects for handling the business logic).
- Do not use filters.
- Return just one instance variable from your actions.

# Testing
- Just test for the proper http code if developing an API

## Active Record
- Reference models within other models for anything else expose a public
  method.
- Wrap methods for create/update/save to define a persistence API for the application.
- Wrap in scopes every query methods for the model.
- Do not throw exceptions from methods return true or false.
- Do not use callbacks be explicit about your methods calls.
- Extend behavior of models using decorators.
- Validations are a good thing to use inside models if they turn out to be too complex
  use a validator object.

# Testing
- Just test behavior avoid testing implementation things like
```
expect(model).to has_one(another_model)
```
are discourage.
- Same thing for validations; test it indirectly through testing behavior.

## Views
- Do not use helpers for formatting data use presenters objects.
- Do not use predicate helper methods in your views for handling conditionals
  use view models.
- Wrap validations for forms within Form Objects.

# Testing
- Do not test views; there's no logic in there

## Conclusion
Probably you will be asking where should I put all the business logic for my
application; well you have a plethora of concepts from the object oriented
design world. You can use interactors or services, decorators, adapters,
presenters, value objects; try to find what's right for your case and implement it.
Remember that Rails is just a framework it is not your application.

## Resources
# Talks
- [Architecture: The Lost Years](https://www.youtube.com/watch?v=WpkDN78P884)
- [Refactoring Fat Models with Patterns](https://www.youtube.com/watch?v=IqajIYxbPOI)

# Articles
- [Design Patterns](https://sourcemaking.com/design_patterns)
- [The Secret to Rails OO Design](http://blog.steveklabnik.com/posts/2011-09-06-the-secret-to-rails-oo-design)

# Books
- [Fearless Refactoring](http://rails-refactoring.com/)
- [Ruby Science](https://thoughtbot.com/books)
