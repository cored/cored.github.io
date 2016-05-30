---
layout: post
title: Test Doubles in Go
date: 2016-05-30T17:10:14-04:00
category: ["TDD", "Go-Lang"]
---

```
// Examples of doing test doubles in Go tests
// Extracted from https://blog.8thlight.com/uncle-bob/2014/05/14/TheLittleMocker.html
type Authorizer interface {
	Authorize(username, password string) bool
}
// Dummy: Pass it around when you don't care how it is use
type DummyAuthorizer struct {}
func (da *DummyAuthorizer) Authorize(username, password string) bool {
		return false
}

// Stub: Use it when you want to test a component on your system in a particular context
type DummyAuthorizer struct {}
func (da *DummyAuthorizer) Authorize(username, password string) bool {
		return true
}

// Spy: Use it when you want to know that the Authorize method was called
type AcceptingAuthorizerSpy struct {
	 AuthorizeWasCalled bool
}
func (as *AcceptingAuthorizerSpy) Authorize(username, password string) bool {
		as.AuthorizeWasCalled = true
		return true
}

// Fake has business behavior. You can drive a fake to behave in different ways by giving it different data.
type FakeAuthorizer struct {}
func (da *FakeAuthorizer) Authorize(username, password string) bool {
		return username == "Bob"
}
```
