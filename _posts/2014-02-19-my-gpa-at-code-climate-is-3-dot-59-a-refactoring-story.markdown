---
layout: post
title: "My GPA at Code Climate is 3.59: A refactoring story"
date: 2014-02-19 10:55
comments: true
categories: ["Ruby", "Rails", "OSS", "Refactoring"]
---

What does that title suppose to mean? Yes that's a very outstanding number I saw that today in a project that I'm working on [LocalSupport](http://github.com/tansaku/LocalSupport). But then I just went to the code and use [Flog](https://github.com/seattlerb/flog) and I found this: 

```ruby
814.0: flog total
8.1: flog/method average

60.9: main#none
35.4: ApplicationController#store_location app/controllers/application_controller.rb:6
33.5: UsersController#update app/controllers/users_controller.rb:7
33.4: namespace(jasmine)::task#ci lib/tasks/jasmine.rake:28
32.1: Organization#none
20.7: CreateOrganizationFromArray#build_organization app/models/create_organization_from_array.rb:40
18.8: OrphansController#create app/controllers/orphans_controller.rb:13
18.3: OrganizationsController#search app/controllers/organizations_controller.rb:7
18.2: namespace(jasmine)::task#server lib/tasks/jasmine.rake:55
17.8: OrganizationsController#update app/controllers/organizations_controller.rb:69
17.4: Organization::add_email app/models/organization.rb:159
17.4: Category::seed app/models/category.rb:12
17.0: Organization#update_attributes_with_admin app/models/organization.rb:50
16.9: OrganizationsController#destroy app/controllers/organizations_controller.rb:82
16.2: ApplicationHelper#smart_truncate app/helpers/application_helper.rb:3
15.3: Organization#remove_errors_with_address app/models/organization.rb:192
14.7: Address#parse app/models/address.rb:3
14.3: CSVHeader#method_missing app/models/csv_header.rb:23
13.9: Organization::import app/models/organization.rb:137
13.8: Organization#generate_potential_user app/models/organization.rb:175
13.6: OrganizationsController#create app/controllers/organizations_controller.rb:51
12.9: ApplicationController#after_sign_in_path_for app/controllers/application_controller.rb:24
12.5: ContributorsController#show app/controllers/contributors_controller.rb:6
12.3: OrganizationsController#show app/controllers/organizations_controller.rb:29
```

So first of all; what does flog do? *Flog reports the most tortured code in an easy to read pain report. The higher the score, the more pain the code is in.* So pain, we don't like that we prefer pleasure above pain of course. So how is this report affecting my pleasure. I would like to add as much features as I can and from time to time the code do some resistance and that's what stop me for finishing 3 features a day. So what can I do about it?

My rule of thumb is just to try to keep the numbers in the left side of the report as low as 10; if there's something above that number probably it's needs a check. As flog is reporting on single methods is easier to just pick one and try to get it in better shape. Let's start with the first one the application_controller.rb in line 6: 

```ruby
 def store_location
    # store last url - this is needed for post-login redirect to whatever the user last visited.
    sign_in = Regexp.new '/users/sign_in'
    sign_up = Regexp.new '/users/sign_up'
    sign_password = Regexp.new '/users/password'
    user_confirmation = Regexp.new '/users/confirmation'
    cookies_allow = Regexp.new '/cookies/allow'
    unless (sign_in =~ request.path ||
        sign_up =~ request.path ||
        user_confirmation =~ request.path ||
        sign_password =~ request.path ||
        cookies_allow =~ request.path ||
        request.xhr?) # don't store ajax calls
      session[:previous_url] = request.path
    end
  end
```

Pretty long method in my honest opinion; so what can we do about it. Well [Long Method](http://sourcemaking.com/refactoring/long-method) is a code smell and normally what I do is to just extract more smaller methods, here is the final code: 

```ruby
def store_location
    # store last url - this is needed for post-login redirect to whatever the user last visited.
    sign_up = Regexp.new '/users/sign_up'
    sign_password = Regexp.new '/users/password'
    user_confirmation = Regexp.new '/users/confirmation'
    cookies_allow = Regexp.new '/cookies/allow'
    unless (sign_in =~ request.path ||
        sign_up =~ request.path ||
        user_confirmation =~ request.path ||
        sign_password =~ request.path ||
        cookies_allow =~ request.path ||
        request.xhr?) # don't store ajax calls
      session[:previous_url] = request.path
    end
  end

private 
def sign_in
   Regexp.new '/users/sign_in'
end
```

Basic stuff; I just used the [Replace temp with query method](http://sourcemaking.com/refactoring/replace-temp-with-query) refactor. Of course I needed to reran my specs so I did and found this: 

```
Devise::SessionsController POST create non-admins associated with nothing LOGS IN redirects to home page after non-admin associated with nothing logs-in
Failure/Error: post :create, 'user' => {'email' => 'example@example.com', 'password' => 'pppppppp'}
ArgumentError:
wrong number of arguments (2 for 0)
# ./app/controllers/application_controller.rb:60:in `sign_in'
# ./spec/controllers/sessions_controller_spec.rb:57:in `block (5 levels) in <top (required)>'

```

NOOO!!!, so I broke the test suite. What I did at this moment was just to rename the extracted method to another name and then all went green again. Good stuff; so let's see what flog says about this refactor: 

```
814.0: flog total
8.1: flog/method average

60.9: main#none
36.3: ApplicationController#store_location app/controllers/application_controller.rb:6
```

What? I did an extract method and I got a higher flog number why is this? I start to feel pain and confirm the slogan for flog. So I went ahead and extract all the others temp variables from it: 

```ruby
def store_location
     # store last url - this is needed for post-login redirect to whatever the user last visited.
     unless (sign_in_url =~ request.path ||
          sign_up_url =~ request.path ||
          user_confirmation =~ request.path ||
          sign_password =~ request.path ||
          cookies_allow =~ request.path ||
          request.xhr?) # don't store ajax calls
          session[:previous_url] = request.path
     end
end

private
def sign_in_url
     Regexp.new '/users/sign_in'
end
 
def sign_up_url
     Regexp.new '/users/sign_up'
end
 
def sign_password
     Regexp.new '/users/password'
end
 
def user_confirmation
     Regexp.new '/users/confirmation'
end
 
def cookies_allow
     Regexp.new '/cookies/allow'
end
```
Flog?

```
814.0: flog total
8.1: flog/method average

60.9: main#none
37.4: ApplicationController#store_location app/controllers/application_controller.rb:6
```

And then I cried. When I stopped just hitting my head I went to Flog's site again and read a little bit. Flog is a complexity metric tool; and the temp's did not have any type of complexity the fact that I put the temp's values in another place just increase the visibility for flog on that method for the real problem. The conditional stuff was the real problem; so I refactor that out also: 

```ruby
def store_location
# store last url - this is needed for post-login redirect to whatever the user last visited.
     unless (request_path_includes?(sign_in_url) ||
          request_path_includes?(sign_up_url) ||
          request_path_includes?(user_confirmation) ||
          request_path_includes?(sign_password) ||
          request_path_includes?(cookies_allow) ||
          request.xhr?) # don't store ajax calls
          session[:previous_url] = request.path
     end
end

private 
...

def request_path_includes?(url)
  url =~ request.path
end
```

Flog?

```
21.9: ApplicationController#store_location app/controllers/application_controller.rb:6
```

And the happiness and pleasure came back. 

## Conclusion

Probably another person would spot the exact problem from the start; but the extraction of the temps to particular methods gave me a better glance of the entire code structure and help me figure it out the best way to compose the other method. I don't think Code Climate is a bad tool but sometimes we need to check deeper and with others tools to keep getting our code base in shape. I hope this article helps you folks to keep getting your code in shape and just don't felt into the trap of Code Climate's high GPA :-P 
