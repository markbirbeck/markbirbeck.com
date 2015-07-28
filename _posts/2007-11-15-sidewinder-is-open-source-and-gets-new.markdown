---
layout: post
title: "Sidewinder is open source and gets a new home"
date: 2007-11-15
comments: false
---
The transition of Sidewinder, our semantic web applications viewer, to an open
source project is now complete. It took quite a lot of work in a number of
different areas, but we're convinced that this will now help us realise the
full potential of the framework.

<!-- more -->

  
The first thing that had to be done was of course to ready the code. [Phil
Booth](http://www.ohloh.net/accounts/10639), the lead developer on the viewer,
put a lot of work into general tidying up, reordering components for
Subversion, and so on. He also did some important work that takes us in a
cross-platform direction, such as removing our dependency on MFC, and putting
the platform on top of wxWidgets.

  
Alongside this work, we also had to spend time looking at the best way to host
the code, how to support bug-tracking, where to put discussion forums, and so
on. We looked initially at the environments like SourceForge and Google Code
that do everything for you, but in the end we liked the look of Trac so much
that we felt that was the way to go. And since there seemed to be a number of
advantages to hosting our own SVN repositories, we ended up with the following
arrangement:

  * an Amazon EC2 instance running:
    * Drupal for the [main Sidewinder Viewer site](http://www.swcube.com/);
    * Trac for the [Sidewinder Viewer developer site](http://sw.swcube.com/);
    * and SVN for the [Sidewinder Viewer source repository](http://sw.swcube.com/svn/);
  * a [SourceForge project](http://sourceforge.net/projects/sidewinder/) to host the downloads;
  * two Google Groups for discussions--one for [developers that _use_ Sidewinder](http://groups.google.com/group/sidewinder-users/topics), and one for [developers who are working on Sidewinder](http://groups.google.com/group/sidewinder-dev/topics).
  
So far this arrangement is working pretty well, and in particular Trac and SVN
are a fantastic match.

  

## Getting started with Sidewinder

The fastest way to get started with Sidewinder is to look at the post [The 10
minute guide to Sidewinder (or 'How to turn a web app into a desktop app
without programming')](http://www.swcube.com/swviewer/intro) on the swcube
site. Not only does it show how to run a web application like Google Reader in
its own window, docked to the side of the screen, and set to auto-hide, but it
also shows how to turn an iPhone application into a desktop gadget.

  
Sidewinder can also run embedded in the browser, execute JavaScript
applications from the command line, and more, but its ability to run web
applications as standalone applications is such a useful feature that it's an
excellent place to start.

