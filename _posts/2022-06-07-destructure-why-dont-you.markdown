---
layout: post
title:  "Destructure, Why Don't You"
date:   2022-06-07 14:08:00 +0000
comments: true
tags: 
 - javascript
---

There is nothing really wrong with this:

```js
const names = users.map(
  /**
   * @param {User} user - A user
   */
  (user) => user.name
)
```

We're taking an array of people objects and creating a new array that contains the value of the `name` property from each person.

However, there are a couple of reasons that we might prefer something slightly different.

The first is that we have now introduced a named variable that serves no purpose other than being something on which we can hang our selection of the `name` attribute. We have created a variable called `user` only so that we can then perform `user.name`.

This is distracting, partly because we need to look at `user` when looking at the code (since we don't know whether it represents something important), but also because we now really need to have our wits about us when reviewing--the function could do anything to the `User` instance and we need to be really sure about whether it's intentional or not.

If this was the only way we could achieve what we wanted then it would be fine, and we'd just move on.

But in recent versions of JavaScript it is possible to achieve the same end without introducing the extra variable. We can do this by using destructuring.

A version of our function that uses destructuring would look like this:

```js
const names = users.map(
  /**
   * @param {% raw %}{{ name: string }}{% endraw %} name - A name of something
   */
  ({ name }) => name
)
```

With this approach the only value that is available to the function is `name`--it does not have access to the full object. It's pretty clear to anyone reviewing this code that we don't intend to do anything other than extract the `name` property.

What's more, the type of the object passed in to the function is 'anything that has a `name` property'. Our previous function could only accept something of type `User`, but with our new function we can accept many different types of object, as long as they have a `name`:

```js
/**
 * Get the name from any object that has one.
 * @param {% raw %}{{ name: string }}{% endraw %} name - A name of something
 */
const getName = ({ name }) => name

const userNames = users.map(getName)
const countryNames = countries.map(getName)
const filmNames = films.map(getName)
```

We've only made a small change, but by using destructuring we've obtained some really important benefits: we've helped our code reviews, deepened our understanding of our function and made it easier to manage in the future.