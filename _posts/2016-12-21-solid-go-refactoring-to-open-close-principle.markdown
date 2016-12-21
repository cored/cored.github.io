---
layout: post
title: SOLID GO - Refactoring to Open/Close Principle
date: 2016-12-21T07:07:03-05:00
category: ["Refactor", "Go-Lang", "SOLID"]
---

This is a continuation of my series of articles related to the SOLID
principles and how they apply to Go programs.  In this entry I will try to
explain how I refactored a piece of functionality to make it open for extension
and closed for modification.

But first, what does that mean?

# Open/Closed Principle

The formal definition of this principle goes as follows:

_You should be able to extend the behavior of a system without having to modify that system._

However, in practice, what exactly does that mean? Let's look at some example
code which happens to be in production right now. For the sake of simplicity I removed the logic
related to the persistence side of things.

```go
func (s *Store) CreateUnlockToken(userID int64) (string, error) {
	// ...
	tokenGenerator := util.NewTokenGenerator(s.cfg.TokenPepper)
	rawToken, encToken, err := tokenGenerator.GenerateUnlock()
	if err != nil {
		return "", err
	}
	// ...
}
```

What this code is trying to do is to create and persist a token in
the database for a given user. At first glance this code looks ok. But looking
closer we can see some issues:

* There's no way to test the token generation isolated from the persistence
logic, forcing the test to integrate with the database every time you want to
test that logic.

* If I want to reuse this token I will have to duplicate the code.

* The `GenerateUnlock` function is returning three types which implies doing
more than one thing thus breaking the [Single Responsibility Principle](http://cored.github.io/blog/2016/09/26/following-solid-in-go-srp)

Let's try to address those issues. By introducing an interface we are
going to make the `CreateUnlockToken` function depend on an abstraction instead of
a concrete type and remove the coupling between the store and the token generator:

```go
type Token interface {
	Raw() string
	Encoded() string
}

type UnlockToken struct {
	pepper string
	raw string
	encoded string
}

func NewUnlockToken(pepper string) (UnlockToken, error) {
	// Login to handle the token pepper and return a new UnlockToken
	// We are going to return an error just in case something happen while
	// generating the unlock token

	return UnlockToken{raw: generatedRaw, encoded: generatedEncoded}, nil
}

func (ult UnlockToken) Raw() string {
	return ult.raw
}

func (ult UnlockToken) Encoded() string {
	return ult.encoded
}

func TestUnlockToken(t *testing.T) {
	t.Run("when generating a valid unlock token", func(t *testing.T) {
		// ... assert for raw and encoded
	})
}
```

And now the new implementation for the store goes like this:

```go
func (s *Store) CreateUnlockToken(email string, userID int64, token Token) (string, error) {
	// ...
	// use the token data by calling token.Raw() or token.Encoded()
}
```

And the test will look like this:

```go
type FakeUnlockToken struct {
	MockedRaw func() string
	MockedEncoded func() string
}

func (fut FakeUnlockToken) Raw() string {
	return fut.MockedRaw()
}

func (fut FakedUnlockToken) Encoded() string {
	return fut.MockedEncoded()
}

func TestCreateUnlockToken(t *testing.T) {
	t.Run("when passing an invalid token", func(t *testing.T) {
		postgresStore := NewPostgres()
		fut := FakeUnlockToken{
			MockedRaw: func() string {
				return "invalid-raw"
			},
			MockedEncoded: func() string {
				return "invalid-encoded"
			},
		}

		// ... persistence assertions
	})
}
```

This looks better now so I guess we can close our editors go home and chill,
right? Wrong. There's something missing from this code which is the fact that
we need to instantiate our new `UnlockToken`. Since the new `CreateUnlockToken`
depends on a token and we want to inject it, let's make the client of that
function be the one instantiating the token.

```go
func (s *Server) CreateUnlockToken(...) {
	s.store.CreateUnlockToken(req.Email, req.UserID, NewUnlockToken{s.cfg.TokenPepper})
}

```

Voila! With this small refactor we changed the inner components of the code to
depend on an abstraction and now we can swap implementations of tokens.

The open/closed principle helps us by reducing the coupling between components and
as a consequence we will have maintainable code bases that are easier to extend and change
in the future.

Thanks to [Jeff Federman](https://github.com/jefffederman) for the proof reading :-)
