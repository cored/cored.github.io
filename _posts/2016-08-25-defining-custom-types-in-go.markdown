---
layout: post
title: Defining custom types in Go
date: 2016-08-25T08:42:34-04:00
category: ["GoLang", "DDD"]
---

As Alan Perlis once said:
_The string is a stark data structure and everywhere it is passed there is much
duplication of process. It is a perfect vehicle for hiding information_

Let's explore this issue with some code extracted from an OSS project
[Broadway](http://github.com/namely/broadway) Namely's deployment tool for staging environments.

Broadway has the concept of an instance which is the data representation of what the
system uses to deploy services into [Kubernetes](http://kubernetes.io).

Here is the definition of that data:

```
type Instance struct {
  PlaybookID string
  ID string
  Status
}

func (i *Instance) Path() string {
  return fmt.Sprintf("%s/instances/%s/%s", env.EtcdPath, i.PlaybookID, i.ID)
}

```

The main issue with this implementation is duplication. If a programmer needs
a `Path` for others use cases he will have to replicate the same string formatting
every time. The problem with this is the duplication of information.

The [don't repeat yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) principle states to have only one representation of
any concept in the system; abstractions like this make new concepts evident.

How can we solve that issue? One approach would be to create a function
to do the formatting, like so:

```
func BuildPath(PlaybookID, ID string) string {
  return fmt.Sprintf("%s/instances/%s/%s", env.EtcdPath, PlaybookID, ID)
}
```

And that will reduce the coupling between the data and the behavior. However if
we take a look a little bit closer we are actually hiding is an important concept
from the domain which is a `Path`.

How do we know that we are hiding an important concept from the system? To
answer that question let's take a look to another code example:

```
func ThatExpectAPath(path string) {
  // do something here with a Path
}
```

The issue here is that the function is expecting a `string` as an argument not
specifically a `Path` type which is the actual concept that this function needs.
The reason why this is an issue is  because you could pass any string and the compiler
won't complain. Wouldn't be better if you have a safety net in place to force you to
look the proper way for formatting a `Path`? Well you have it since you are using an
static typed language. The only piece missing is the new type definition which for this
particular case at least the naming is evident; let's see this in action:

```
type Path struct {
  PlaybookID string
  ID string
  RootPath string
}

func (p *Path) String() string {
  return fmt.Sprintf("%s/instances/%s/%s", p.RootPath, p.PlaybookID, p.ID)
}

func ThatExpectAPath(path Path) {
  // do something here with a Path
}
```

The two main benefits of this approach are:

* [Separation of concerns](;t: l  //en.wikipedia.org/wiki/Separation_of_concernsss), now every time that we want to change something in the
system we won't need to hunt down for different ways for creating a new `Path`
we are going to look at the type and do what we need according to that definition.

* [Higher level of abstraction](), the only way that we humans have to avoid
complexity is to work on a higher level of abstraction. We can get more done by
combining different components rather than manipulating variables and control
flows.

What about creating the wrong abstraction? The answer for that one is easy;
just rollback into the old code and start over. It will be easier for the client
to remove a type and start with duplication than to hunt that code down and remove
it when there's no obvious starting point.

How does this small piece fit with the rest of the system? Now we can use the
embedded type mechanism of Go and compose our old type.

```
type Instance struct {
  ...
  Path
}
```

With that in place we can keep using the same formatting across the system and
have the safety net of the compiler when passing that concept around.

By introducing this custom type in the system we decrease the translation cost
between [business concepts and implementation details]( http://martinfowler.com/bliki/UbiquitousLanguage.html) making it easier for developers
to compose better abstractions with smaller reusable components.
