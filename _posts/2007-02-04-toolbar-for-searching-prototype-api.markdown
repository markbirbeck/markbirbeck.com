---
layout: entry
title: "A toolbar for searching the Prototype API documentation"
date: 2007-02-04
comments: true
---
[![formsPlayer toolbar for searching Prototype API docs](http://farm1.static.f
lickr.com/128/380050989_f83c6bccfc_t.jpg)](http://www.flickr.com/photos/mark-
birbeck/380050989/)

  
A recent announcement that [documentation for the Prototype Ajax
library](http://prototypejs.org/api/) was [now
available](http://prototypejs.org/2007/1/18/prototype-documentation-is-here)
was followed almost immediately by a [rather neat looking desktop
gadget](http://www.bartelme.at/journal/archive/prototype_reference_widget/)
and a Firefox sidebar. Not to be outdone, I decided to put together a toolbar
for Internet Explorer, and managed to do the whole thing with one XForms form
control and an action handler--and of course, no script.

<!-- more -->

  
If you use Prototype and you just want to install the documentation toolbar,
you can do so [from here](http://svn.x-port.net/svn/public/applications
/prototype-documentation/toolbar-installer.html). You'll need formsPlayer, but
this form will install it automatically, and once you have it you'll be able
to add other bars to IE without any further downloads.

  
I've written the whole thing up as a [tutorial](/how-to/toolbar-prototype)
which might prove interesting even if you don't want to create any toolbars,
as it shows different ways to use `load`, as well as `DOMActivate` on an
`input` control. If you'd like to get the code and use it as a guide to
creating your own toolbars, it is available [from
here](http://svn.x-port.net/svn/public/applications/prototype-documentation/).

