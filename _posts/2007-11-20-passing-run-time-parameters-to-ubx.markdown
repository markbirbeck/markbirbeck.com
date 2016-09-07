---
layout: entry
title: "Passing run-time parameters to UBX Viewer, via the URL"
date: 2007-11-20
comments: true
---
The Ubiquity Viewer contains a growing number of innovative features, and one
I'd like to highlight here is the ability to pass run-time parameters for a
web application, via a URL.

  
Note: This post originally referred to 'Sidewinder', which is now part of the
Ubiquity Browser Extension framework, or UBX. (See [Sidewinder is dead, long
live UBX](http://webbackplane.com/node/99).)

<!-- more -->

  

## `meta` and `link` values

You'll probably already know that [the UBX viewer can make use of values in
the HTML `meta` element](/node/175) to set things like its initial position,
the size and width of the initial window, opacity and transparency, and so on.
The ability to place important information like this into the document itself
is a simple but powerful technique, since it means that you don't need to
create manifests containing configuration information, giving you one less
file to maintain and deploy.

  
However, whilst this technique is simple and straightforward when you are
building your own application, what happens when you want to run someone
else's web application on your desktop? Or what should you do if you want to
take the map display you've created and re-use it as a large desktop
application one minute and a small gadget the next? Surely we don't have to
create two documents that are exactly the same except for the height and
width?

  
The answer to both of these question is to move some (or all) of the run-time
parameters that are in the head of the document, out to the end of the URL. To
do this we use the `meta` and `link` XPointer schemes.

  

## Google Reader

For example, let's say that you wanted to load Google Reader into a window
that was 900 by 500, positioned at the top of the display, and that would
autohide when you moved the mouse away; this is easily achieved by slightly
modifying the URL used to open Google Reader in the UBX viewer, as follows:

    
    swviewer2 http://www.google.com/reader#meta(width=900,height=500,autohide,position=top)

  
  
The result would be something like this:

  
![Screenshoot of Google Reader running in Sidewinder](/files/google-reader-
larger.png)

  
Similarly, what if you wanted to run the Facebook iPhone application as a
gadget on your desktop, with no chrome, docked to the side, and using the same
dimensions as an iPhone? As before, take the base URL of the iPhone
application, and then add the relevant `meta` parameters before passing the
whole thing to the viewer:

    
    swviewer2 http://iphone.facebook.com#meta(width=320,height=480,chrome=false,autohide,position=right-top)

  
  
The result would be something like this:

  
![Screenshot of Facebook iPhone application running in Sidewinder](/files
/iphone-facebook-news.png)

  

## Command-line parameters

Putting parameters into the URL like this essentially places the command-line
parameters that one would ordinarily expect to use when running an application
--such as `blah.exe -height=500 -width=900`--into the URL. This in turn has
the important effect of 'factoring out' the application that _acts on_ the
URL, by which I mean that the application is now transparent to the whole
process of running our web application.

  
That might seem a little obtuse, but the point I'm making is that by using
XPointer frameworks like this, we have leveraged perhaps [the most important
invention of the web, the URI](http://xml-applications.blogspot.com/2007/11
/2w-second-coming-of-web.html). And just as URIs are often used as a universal
document identifier, independent of any browser used to view that document, so
we use URIs as a 'universal command-line', independent of any web applications
processor that might process that command-line.

  

## User control

But this only tells half the story. For me there is something even more
exciting, which is that this technique puts control over the use of an
application into the hands of the people running it. For example, we just saw
how the size and position of the Google Reader web application can be set in
the URL with the `meta` XPointer framework, but we could go further than that
and use the `link` XPointer framework so that when we run the application we
use a different stylesheet. We could even refer to a script, some RDFa, and so
on.

  

## Next steps

The next step with this work is to start to formalise the concept, so that it
can be used in many different circumstances. For example, although we've seen
here how the technique can be used when running web applications with the
Sidewinder Viewer, it would also be possible to use run-time parameters when
loading widgets into frameworks such as iGoogle, passing in parameters such as
preferred colour-scheme, nearest city, and so on. By having some kind of
specification it will also make it possible for others to comment and provide
input on how to take this work forward.

  

## Further reading

There are more examples of how the XPointer frameworks can be used, in [The 10
minute guide to the UBX viewer (or 'How to turn a web app into a desktop app
without programming')](/swviewer/intro). For a more in-depth look at running
the UBX viewer as a web applications viewer, see [UBX Viewer as a web
applications viewer](/node/516).

