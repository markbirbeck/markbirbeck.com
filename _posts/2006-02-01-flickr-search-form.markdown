---
layout: entry
title: "A Flickr Search Form"
date: 2006-02-01
comments: true
tags:
 - xforms
 - flickr
 - web 2.0
 - webapps
 - ajax
 - programming
 - sidewinder
 - browser
---
Producing a Flickr search form is fast becoming the "Hello, World!"
application for Web 2.0. Kevin Lynch did a Flex demo at the Web 2.0 conference
that searched Flickr, and then the Ruby on Rails guys followed suit with an
Ajax lookalike. Our XForms version is part of the _Introduction to XForms_
tutorial, and has a rich UI like the others...but since XForms extends HTML,
then this Flickr search form needs no scripting or server-side assistance.

<!-- more -->

## Lookalikes

The first Flickr search form I saw was on the Macromedia site, where [Sho Kuwamoto used Flex to create a Flash application that searches Flickr](http://labs.macromedia.com/wiki/index.php/Flex_Framework:samples:photodemo)
for some images and then displayed the results in a dynamic panel. ([Kevin Lynch had already done the same demo](http://weblogs.macromedia.com/mesh/archives/2005/10/video_of_kevin.cfm)
as Sho, in front of a live audience at the Web 2.0 conference.)

  
The guys over at Ruby on Rails then [used the same idea](http://www.rubyonrails.org/screencasts)
to show how a Rails back-end could do the searching before using a trusty HTML
front-end (with a sprinkling of Ajax magic) to get some of the animated effects
that Flex was getting from Flash.

So of course we had to [do the same](http://www.youtube.com/watch?v=KcNN9u4Z_Yw).
Actually, we go a little further and show how to use one XForms attribute to
get dynamically resizing images when the user drags a slider!

(Apologies for the quality--YouTube distorts the videos a little, as it
resizes them.)

## Rich-client language

The XForms approach to a Flickr search form is obviously very different to
that taken by architectures like Flex or Rails, for two reasons. First, we can
talk directly to Flickr, without setting up a server, creating scaffolding,
and so on. And second, the rich client language is right in the heart of the
browser.

This means that unlike Flex, which is pre-compiled to produce Flash and then
deployed from a server, or Ruby on Rails which requires special tags and
templates on the server to produce the user interface, the XForms UI is a
separate logical entity for your project, making it far easier to manage.

This creates many new possibilities; we can use general purpose workflow
engines, XML data stores, RDF databases, web services, Web 2.0 APIs, RSS feeds
and so on, all without having to deeply connect your UI to some server-side
architecture--which is what _skimming_ is all about. (Which means you could
easily use Rails to manage a database _without_ having to mix in the user
interface at the same time.)

## Edit, save, refresh

By decoupling the data and the user interface, you can start to develop rich
client applications immediately, without having to change your current
servers. Which means that if your application architecture can currently
deliver HTML pages, then it can immediately start delivering rich, responsive
XForms.

The skim approach means that provided you have a reasonable grasp of HTML and
CSS you can take the code in [the tutorial](/node/89), save it to your
desktop, and start editing.

Editing, saving, refreshing--right in the browser, without the need for a
server...isn't that how you learned HTML all those years ago?
