---
layout: post
title: "webdriver-mocha: Using Selenium Webdriver with Mocha for End-to-end Testing"
date: 2016-01-04
comments: true
tags:
 - end-to-end tests
 - mocha
 - selenium
 - tdd
 - web apps
 - webdriver
 - webdriver-mocha
---
Although I'm always using Mocha to test my Node modules I've not had much cause recently to use it to write tests that run in a browser. Now that I need to, I find a lot of new testing apps vying for my attention, but most of them want me to move away from Mocha. I looked at them...and then decided to create my own module which allows me to stick with Mocha and still get to use Selenium. Here's how I did it.

<!--more-->

## XForms

I say I haven't "recently" needed to write browser tests because there was a time when I was doing nothing but. Some years ago, working on a JavaScript [XForms](http://bit.ly/2y5TZ0) framework (called variously [backplanejs](http://bit.ly/1OQnwvB) and [Ubiquity XForms](http://bit.ly/1OQnNP4)) with [a team comprising guys from IBM and colleagues from my own company (x-port.net)](http://bit.ly/1OQnK5K), we ran a mountain of tests with Selenium.

It was a great environment once it was working, but to get it to run we had Ant files that first launced Selenium servers, then invoked browsers and pushed tests through, before finally closing everything down. This was the best way we found to be able to test on many environments (Ant is great at being cross platform) and in many browsers (Selenium does a good job of allowing access to all the main browsers).

Fast forward to today, and I'm more than happy to use the latest and greatest tech to wrap Selenium. However, after a bit of evaluation it turned out that I might as well just stick with using Selenium directly. I'll explain what I wanted to achieve which will hopefully explain why I stayed closer to the metal.

## Sticking with Mocha

My big priority was that I wanted to run the tests from Mocha. In other words I wanted to be able to simply type `mocha` (or `npm test`) and have any tests that I wanted to set up all run together. I didn't want to differentiate between pure JavaScript unit tests and HTML end-to-end tests -- I might *run* the tests at different times, but how they are written and how they are run should be the same regardless of the type of test.

Although [Protractor](http://bit.ly/1OQnZOo) looks great, for me it failed on this first hurdle; although I can specify the framework to use in the configuration -- `jasmine`, `mocha`, etc. -- I'd still need to run the tests with the command `protractor`. I know that's not the most onerous thing that could happen, and I could even combine the `mocha` and `protractor` commands in the `npm test` script:

```
"scripts": {
  "test": "mocha && protractor"
}
```

But this didn't really feel right to me. I've chosen to use Mocha as my test-runner, so why shouldn't I be able to use it to run all of my tests?

## Running Selenium Webdriver from Mocha

As it happens, the code to run Selenium Webdriver is pretty straightforward, and looks something like this ([sample code lifted directly from the SeleniumHQ site](http://bit.ly/1OQowzJ)):

```javascript
var webdriver = require('selenium-webdriver'),
    By = require('selenium-webdriver').By,
    until = require('selenium-webdriver').until;

var driver = new webdriver.Builder()
    .forBrowser('firefox')
    .build();

driver.get('http://www.google.com/ncr');
driver.findElement(By.name('q')).sendKeys('webdriver');
driver.findElement(By.name('btnG')).click();
driver.wait(until.titleIs('webdriver - Google Search'), 1000);
driver.quit();
```

Grabbing the initialisation part of the code, a Mocha test of this blog would look something like this (note the addition of Chai's `should` module, at the top):

```javascript
'use strict';
require('chai').should();

let webdriver = require('selenium-webdriver');
let By = webdriver.By;
let until = webdriver.until;

let driver = new webdriver.Builder()
  .forBrowser('firefox')
  .build();

describe('my blog', function() {
  it('should navigate to post', function(done) {
    driver.get('http://markbirbeck.com/')
    .then(() => driver.getTitle())
      .then(title => title.should.equal('Mark Birbeck\'s Blog'))
    .then(() => driver.quit())
    .then(() => done())
    .catch(error => done(error))
    ;
  });
});
```

(For more examples see [Writing Tests](http://bit.ly/1OQozM4) and [Promises](http://bit.ly/1OQoF6r) in the [WebDriverJS User's Guide](http://bit.ly/1OQoF6r).)

## Improvements to the Template

There are four improvements that we'd like to make to this basic template:

* avoid having to nest every WebDriver command in a simple function (for example, ```() => driver.getTitle()```);
* be able to use promise-related libraries like [Chai as Promised](http://bit.ly/1OQrbtj);
* be able to dispense with `done()` and `catch()` in Mocha by returning a promise;
* be able to use `driver`, `By` and `until` without having to repeat the declarations at the top of every test file.

### Using Promises

The first three points *should* all be possible, but since the promises that WebDriver returns are not 'compatible' with those used in other libraries -- such as Mocha and Chai as Promised -- they all fail.

For example, to use WebDriver commands like `getTitle()` we are obliged to nest them in a simple function:

```javascript
    .
    .
    .then(() => driver.getTitle())
      .then(title => title.should.equal('Mark Birbeck\'s Blog'))
    .
    .
```

However, what we *should* be able to do is simply pass a reference to the WebDriver function, like this:

```javascript
    .
    .
    .then(driver.getTitle)
      .then(title => title.should.equal('Mark Birbeck\'s Blog'))
    .
    .
```

Although WebDriver claims to return promises for commands the type of promises being used are not compatible with other libraries -- like Mocha -- and what should be a chain of promises turns out not to be (i.e., the promises are not being 'followed'). To get this to work we have to wrap calls to WebDriver functions with a more 'standard' promise:

```javascript
    .
    .
    .then(() => Promise.resolve(driver.getTitle()))
      .then(title => title.should.equal('Mark Birbeck\'s Blog'))
    .
    .
```

To make this wrapping easier, I've created the `webdriver-mocha` module which amongst other things, wraps all of the WebDriver commands with 'proper' promises and therefore allows this more direct syntax.

Note that doing this wrapping makes it possible to use Chai as Promised, which won't work with the promises that `WebDriver` is using. It also means that we can take advantage of a key benefit of Mocha which is that if a test returns a promise then Mocha will handle rejections. This means we can dispense with calling `done()` to terminate the test, and avoid the need to provide the `catch()` handler.

### Easy Inclusion in Mocha

Rather than having to declare the imports and variables for WebDriver at the top of each test file, we'd like to incude them as globals to Mocha. The `webdriver-mocha` module also facilitates that, making `driver`, `By` and `until` available.

Note that since this module takes responsibility for creating `driver` it also takes responsibility for calling `driver.quit()` when all tests are complete.

## An Example

The example we began with can now be expressed like this:

```javascript
var chai = require('chai');
chai.use(require('chai-as-promised'));
chai.should();

require('webdriver-mocha');

describe('my blog', () => {
  it('should navigate to post', () => {
    return driver.get('http://markbirbeck.com/')
    .then(driver.getTitle)
      then(title => title.should.equal('Mark Birbeck\'s Blog'))
    .then(() =>
      driver.findElement(
        By.linkText('A Mixin Approach to Material Design Lite Using Sass')
      ).click()
    )
    .then(
      driver.getTitle()
      .should.eventually.equal('A Mixin Approach to Material Design Lite Using Sass')
    )
    ;
  });
});
```

Note the addition of Chai as Promised as an alternative way to test the promises, the removal of `driver.quit()`, and -- thanks to the `return` statement at the start of the test -- the omission of the two `done()` handlers.

Loading the `webdriver-mocha` module has given us:

* access to `driver`, `By` and `until` in exactly the same way as many snippets of code to be found on the web, making it easy to include them, and;
* the use of 'proper' promises on the commands, improving the integration with other libraries that use promises, such as Chai as Promised, and of course Mocha itself.

## More Information

The module is available on NPM as [webdriver-mocha](http://bit.ly/1OQpDPT).