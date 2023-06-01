---
layout: post
title: "Building a Backpack interface with XForms"
date: 2006-04-12
comments: true
tags:
 - xforms
 - web 2.0
 - webapps
 - ajax
 - programming
 - sidewinder
 - browser
---
![Backpack logo](/files/backpack-small.png)

We're getting ready for a new version of Sidewinder and in the process we're
building a few desktop applications to show what you can do with the new features. One of these
applications makes use of Backpack to store tasks and reminders, and during
the course of developing it, Phil decided to set up a simple XForm that can
access every feature of Backpack. It's such a handy little form that we
thought we'd share it with you--if you're planning to build an XForms
application that uses Backpack you'll definitely want to take a look, but the
form has more general use too in that it shows how to use the formsPlayer
extensions to control all aspects of data submission.

<!-- more -->

One problem with XForms' submission is that the URL to interact with is a
straightforward string, but this is no use with the many RESTful APIs that are
emerging, where the resources you want to interact with are encoded in the
URL, rather than with query parameters.

Backpack has three further complicating factors; first, your user name is
actually used as a sub-domain to access your data:

    
    http://yourname.backpackit.com/ws/
    

Second, a specific request header is required -- `X-POST_DATA_FORMAT` must be
set to `xml`. And the third is perhaps the most complicated -- the URLs
themselves need to be dynamic; for example, to update the content of item 5 on
page 1234, the URL will be something like:

    
    http://yourname.backpackit.com/ws/page/1234/items/update/5
    

A submission to Backpack is probably about as complicated as an XForms
submission is ever likely to get, and [Phil's code](/files/backpack-demo.html)
shows how to implement all of these things using the formsPlayer submission
extensions.

(Note that if you don't have formsPlayer installed already this form will
automatically install it using a signed CAB file.)

