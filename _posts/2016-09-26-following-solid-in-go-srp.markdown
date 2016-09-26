---
layout: post
title: Single Responsibility Principle in Go
date: 2016-09-26T17:28:18-04:00
categories: ["GoLang", "Software Craftmanship"]
---

After watching [Dave Cheney's talk about SOLID principles in Go](https://gist.github.com/2ef5f7b69f1eef54ff856ab8beeaf81d),
I wanted to jump into the discussion in a practical way; so I decided to solve
an small problem and see how to apply at least one principle in this case SRP.
The problem that I'm going to solve is to build a data model for
for [a deck of cards](https://en.wikipedia.org/wiki/Standard_52-card_deck).

To make sure that we are going to move in the right path let's try to TDD our
way through this problem.  TDD is a tool for designing software and as a side effect
it provides us with the benefit of automatic correctness checking.

So, How do we start? Let's define some requirements based on the Wikipedia
article.

```
When dealing a new deck
I want to get 52 cards

When I put card in the deck
I want to see a rank and a suit for that card
```

I think this two requirements are good to start and we can always go back to
our product owner and add more stories to the backlog.

Let's implement the first requirement by writing a test. Before dealing the deck we
need to create it; so let's write that test.

```
package main_test

import (
	"testing"

	"github.com/stretchr/testify/assert"
)

func TestNewDeck(t *testing.T) {
	t.Run("when creating a new deck", func(t *testing.T) {
		deck := Deck{}

		assert.Equal(t, len(deck.Cards), 52, "retrieve 52 cards")
	})
}
```

A good practice for naming package for testing is to use the `_test` by using
this nomenclature we will be force to be explicit about external dependencies
by exporting just what we need. Since the test is our first client it make a lot
of sense.

By running the test we get the following error:

```
main_test.go|14| deck.Cards undefined (type main.Deck has no field or method Cards)
```

The compiler is saying that we need to add a new `Deck` struct with a field
name `Cards` exposing the question - what is a card? The way to figure that out
is by adding a new test for it. If we take a closer look at our old test it doesn't
say much about what makes a card be a card. Based on the wikipedia article we know
that a card consist of a rank and a suit. So let's define that test.

```
	t.Run("when creating a new deck", func(t *testing.T) {
		deck := Deck{}
		t.Run("it contains four aces of spades, clubs, diamonds and hearts", func(t *testing.T) {
			aces := []Card{}
			expectedCards := []Card{
				Card{Rank: "ace", Suit: "hearts"},
				Card{Rank: "ace", Suit: "diamonds"},
				Card{Rank: "ace", Suit: "spades"},
				Card{Rank: "ace", Suit: "clubs"},
			}
			for _, card := range deck.Cards {
				if card.Rank == "ace" {
					aces := append(aces, card)
				}
			}
			assert.Equal(t, expectedCards, aces)
		})
	})
```

What we get by running this test is the following error:
```
main.go|4| undefined: Card
```

So let's define the `Card` type:
```
type Card struct {
	Rank string
	Suit string
}
```

After writing the production code the test is showing us our first assertion failure:

```
--- FAIL: TestNewDeck (0.00s)
		--- FAIL: TestNewDeck/when_creating_a_new_deck (0.00s)
				--- FAIL: TestNewDeck/when_creating_a_new_deck/it_contains_four_aces_of_spades,_clubs,_diamonds_and_hearts (0.00s)
				Error Trace:    ns.go:225:
				Error:          Not equal: []main.Card{main.Card{Rank:"ace", Suit:"hearts"}, main.Card{Rank:"ace", Suit:"diamonds"}, main.Card{Rank:"ace", Suit:"spades"}, main.Card{Rank:"ace", Suit:"clubs"}} (expected)
```

Since we need a way to build the inner state of the deck at this point is a good idea
to introduce a proper constructor for the `Deck` to handle the initial state of
the data:

```
var (
	ranks = []string{"ace"}
	suits = []string{"hearts", "diamonds", "spades", "clubs"}
)

type Card struct {
	Rank string
	Suit string
}

type Deck struct {
	Cards []Card
}

func NewDeck() Deck {
	cards := []Card{}
	for _, rank := range ranks {
		for _, suit := range suits {
			cards = append(cards, Card{Rank: rank, Suit: suit})
		}
	}
	return Deck{Cards: cards}
}
```

After making our first test green let's see if we can finish the TDD loop cycle
with a refactor. Our rule of thumbs for doing refactoring is to follow the
SOLID principles.

What can we refactor? One thing that we could do is to introduce better names
for our packages. The `main` package name does not say much about what the
inner components are so let's use the name `deck` instead. By changing the name
of the package we are following SRP by grouping components that change for the
same reason.

```
package deck

var (
	ranks = []string{"ace"}
	suits = []string{"hearts", "diamonds", "spades", "clubs"}
)

type Card struct {
	Rank string
	Suit string
}

type Deck struct {
	Cards []Card
}

func NewDeck() Deck {
	cards := []Card{}
	for _, rank := range ranks {
		for _, suit := range suits {
			cards = append(cards, Card{Rank: rank, Suit: suit})
		}
	}
	return Deck{Cards: cards}
}
```

Good we rename the package name and the test keep passing. Now there's
something else that is showing up and is the fact that we have a name overload
for our constructor we don't need to say that we are creating a new deck if we
are already using the `deck` as a package name so let's rename that
constructor:

```
package deck

var (
	ranks = []string{"ace"}
	suits = []string{"hearts", "diamonds", "spades", "clubs"}
)

type Card struct {
	Rank string
	Suit string
}

type Deck struct {
	Cards []Card
}

func New() Deck {
	cards := []Card{}
	for _, rank := range ranks {
		for _, suit := range suits {
			cards = append(cards, Card{Rank: rank, Suit: suit})
		}
	}
	return Deck{Cards: cards}
}
```

That looks accurate however what about the `Card` struct? Do we really need to
export it? Are we going to use a `Card` without a `Deck` ever? Well to me it
doesn't make sense to use just one a card without the entire deck unless you
are the Joker. So let's not export this structure to the outside world.

```
package deck

var (
	ranks = []string{"ace"}
	suits = []string{"hearts", "diamonds", "spades", "clubs"}
)

type card struct {
	Rank string
	Suit string
}

type Deck struct {
	Cards []Card
}

func New() Deck {
	cards := []Card{}
	for _, rank := range ranks {
		for _, suit := range suits {
			cards = append(cards, card{Rank: rank, Suit: suit})
		}
	}
	return Deck{Cards: cards}
}
```

After doing that change and running the test we get a new compilation error:
```
deck_test.go|14| undefined: deck.Card
```

Here comes the confirmation of the use of `_test` since we are not allowed to
use private members of our package we will need to think in terms of how the
client will use the information that belong to our two data structures. Instead
of initializing an expected list of cards let's define it in terms of it's
formatted data or in other words what a final user will want to know something that
we can build without thinking about too many internal details:

```
func TestNewDeck(t *testing.T) {
	t.Run("when creating a new deck", func(t *testing.T) {
		d := deck.New()
		t.Run("it contains four aces of spades, clubs, diamonds and hearts", func(t *testing.T) {
			aces := []string{}
			expectedCards := []string{
				`ace of hearts`,
				`ace of diaminds`,
				`ace of spades`,
				`ace of clubs`,
			}
			for _, card := range d.Cards {
				if card.Rank == "ace" {
					aces = append(aces, card.String())
				}
			}
			assert.Equal(t, expectedCards, aces)
		})

	})
}
```
After refactoring the test we get the following error:

```
deck_test.go|23| card.String undefined (type deck.card has no field or method String)
```

Because of course we are missing the implementation of the `stringer` interface
let's add that:

```
func (c card) String() string {
	return fmt.Sprintf("%s of %s", c.Rank, c.Suit)
}
```

I think this is a good spot to stop; since the idea was to showcase just
the Single Responsibility Principle.

## Conclusion

As Sandi Metz said `Design is the art of arranging code that needs to work
today, and to be easy to change forever` something that we can do with the TDD
cycle by making a failing test past and then moving the design towards the
SOLID principle through continuous refactor.

