---
layout: post
title: "Why is strong parameters better than other methods?"
date: 2014-02-07 14:39
comments: true
categories: ["Rails"]
---

Before answering this question, we first need to know what is strong parameters and before that we need to know what is mass assignment. 

## Mass assignment 

Is the Rails term for passing a hash of values to an object to be assigned as attributes.
The following are the primary methods  that Rails use for mass assign values; Rails allows the developer to assign values all at once instead of one by one. 
```
Post.new(params[:post])
Post.create(params[:post])
@post.update_attributes(params[:post])
```

Unfortunately this convenience brought a major security problem on Rails applications.

Imagine that you have a form in a web and we use mass assignment to update the attributes on an user model.
```
@user.update_attributes(:username => 'value')
```

Imagine that an attacker make a fake form submission to your application adding new values to that particular parameter list. Leading him without any kind of restriction to gain admin access to your site.
```
@user.update_attributes(:username => 'value',  :password => 'password', :admin => true)
```

For that particular problem Rails came with some mechanisms to filter the attributes that are allow to be mass assign. 

## Mass assignment filtering

* Rails v1-2: Blacklisting of attributes - In programming when you add the things that you don't want to allow to a list is call *blacklisting* the problem was that some people did not understand what was happening and simple ignores it.
* Rails v3: Whitelisting of attributes - In this version Rails just did the opposite instead of allowing everything it blocked everything and the programmer had to decide which attributes to allow for mass assignment. The programmer have to specified if the attribute is protected or public accessible in the model and here where's the downside for this method reside; often when we build web applications we want that the attributes be protected when a normal user submits a form but accessible when an admin submit the forms. 
* Rails v4: Strong Parameters - The final solution is to have strong parameters; can't be turn off with a configuration. They are strong because they are on by default; in some senses they protect you from yourself. How did it solve the protected/accessible form submission issue? Well the code to allowing and disallowing is now handle through the controller which needs to mass assign something instead of the model; this way the programmer can have an ```AdminController``` and ```PublicController```

## How does Strong Parameter is use?

Pretty easy indeed. 

```
params.permit(:first_name, :last_name)
```

Another cool thing about strong parameters is the inclusion of the method ```require``` with this method you are telling Rails that this particular parameter is require for use inside the controller.
Require doesn't permit thought, you should use it in combination with permit.

```
params.require(:person).permit(:first_name, :last_name)
```

Hopefully this explain how are mass assignment a good thing, which are it's downsides and how Rails 4 is solving it. 


