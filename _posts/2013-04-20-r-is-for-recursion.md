---
layout: post
title: "R is for Recursion"
comments: false
tags:
  - A to Z 2013
published: false
---

To understand recursion, first you have to understand recursion.

Recursion is a technique in programming (and presumably further afield) that can be amazingly useful, but a little difficult to get your head around. It’s when a piece of code calls itself, performing a nested operation.

Or in English, think of a basic family tree. We have the StartingPerson, and we need their parents.

> I’m going to be using some pseudo-code to help explain it. It’s not a specific language, but will hopefully be readable to non-programmers.

```
Mother = StartingPerson->getMother
Father = StartingPerson->getFather
```

But what about the next level? Well we could do

```
Mother = StartingPerson->getMother
MaternalGrandMother = StartingPerson->getMother->getMother
MaternalGrandFather = StartingPerson->getMother->getFather
Father = StartingPerson->getFather
PaternalGrandMother = StartingPerson->getFather->getMother
PaternalGrandFather = StartingPerson->getFather->getFather
```

And the next level? We’re already into very messy code. So we can change the technique. Any person will have a mother and father, so if we change the focus of the starting person, we can now do

```
CurrentPerson = StartingPerson
Mother = CurrentPerson->getMother
CurrentPerson = Mother
MaternalGrandMother = CurrentPerson->getMother
```

Now we have the same call twice, `CurrentPerson->getMother`. In programming terms, this means we can start to refactor the code. If a call is used more than once, it can be re-jigged to avoid repeating yourself.

```
getMotherRecursive = function(CurrentPerson)
    return CurrentPerson->getMother
end function

Mother = getMotherRecursive(StartingPerson)
```

This creates a new function that we can call repeatedly. StartingPerson is then passed in to the function and becomes CurrentPerson. The “return” line then sends the data back, so Mother = StartingPerson->getMother.

Now for the complicated bit, we also want the Mothers Mother.

```
getMotherRecursive = function(CurrentPerson)
    return getMotherRecursive(CurrentPerson->getMother)
end function

Mothers = getMotherRecursive(StartingPerson)
```

Here’s how this would work

* getMotherRecursive is called with the StartingPerson
* The return value is the value of getMotherRecursive for the StartingPersons Mother
* So getMotherRecursive is called again, now CurrentPerson = Mother
* The return value is the value of getMotherRecursive for the Mothers Mother
* So getMotherRecursive is called again, now CurrentPerson = MaternalGrandMother
* The return value is the value of getMotherRecursive for the MaternalGrandMothers Mother
* So getMotherRecursive is called again, now CurrentPerson = MaternalGreatGrandMothers Mother

By using a recursive call, we can go down as many depths as we want, with only four lines of code. There are a few problems to be aware of when using recursion though.

Firstly, how do you return your data? You could make a list, so “return CurrentPerson + getMotherRecursive(CurrentPerson->Method)” would give something like “Me, Mother, GrandMother, GreatGrandMother”. You should always be getting back a group of data instead of a single value.

And secondly, when does it stop? This code can go back 10 generations. 100 generations. Back to when we crawled from the primordial ooze! A common problem programmers hit when first using recursion is not putting a limit on the call. This will cause the script to never finish, closely followed by threats issued to the computer. Maybe a check on the Date of Birth of the Mother, if it’s past 1800 then don’t return another mother, return the end.

This is only meant as an introduction to recursion, it’s a tricky subject to understand. Like previous A-to-Z challenge posts, I’m preparing a far more technical version of this for release afterwards targeted at PHP programmers.
