---
layout: post
title: "How to Identify More Than One Level of Abstraction in a Method"
date: 2015-01-12T21:46:03-05:00
tags: ["OOD", "Ruby", "Rails"]
---

Here is the spec: 

``` ruby
context "when the access token has expired" do
  it "refreshes the access token and returns the new one" do
   old_access_token = "my-old-access-token"
   new_access_token = "my-new-access-token"
   expiry_time = 899
   user = build(
     :user,
     access_token: old_access_token,
     access_token_expiry: 5.minutes.ago,
   )
   authenticator = double(
     "authenticator",
     refresh_access_token:  {
       "access_token" => new_access_token,
       "expires_in" => expiry_time.to_s,
     }
   )

   token = user.fresh_access_token(authenticator)

   expect(token).to eq new_access_token
   expect(user.access_token).to eq new_access_token
   expect(user.access_token_expiry).
     to be_within(1.second).of(expiry_time.seconds.from_now)
  end
end
```

Testing the following method: 

``` ruby
def refresh_access_token(authenticator)
  tokens = authenticator.refresh_access_token
  self.access_token = tokens.fetch("access_token")
  self.access_token_expires_in = tokens.fetch("expires_in")
  save
end
```

The error message was very informative saying that the method
`refresh_access_token` in the `Authenticator` class was expecting an argument
and here this method is not calling it properly. 

As you can see from the spec there is no way of catching this error because the
spec is mocking a collaborator which we have control on. The `Authenticator`
class. Remember *Do not mock objects that you own*. 

Ok, ok, I'm talking too much about the problem in the code but
how is this telling me that there are more than one level of abstraction in the method? 
Well to fix the error I just stop mocking the `Authenticator` and start using it from the
user spec after that a new error emerge and it was a call to the
`Namely::Authenticator` class which is part of the
[namely-client](http://github.com/namely/ruby-client.git) what does that mean?
Well that we are doing two different unrelated things... Ok, I'm still saying
the same thing without explaining. 

First, the user is trying to do an http request to Namely's API for getting
authentication and then is trying to do persistance logic in the same method...
Can you see it now? Well is pretty obvious how to solve this, we just need to
separate the logic in two different methods right? wrong!!! we could add a new
private method but the underlying logic of this new method will expose that we
have more unrelated logic inside the model pointing once more that we need
a new abstraction. At least that's what I see and that's what I'll be working
for my next set of refactoring for this code.  
