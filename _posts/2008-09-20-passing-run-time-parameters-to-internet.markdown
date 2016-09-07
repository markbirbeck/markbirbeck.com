---
layout: entry
title: "Passing run-time parameters to internet applications"
date: 2008-09-20
comments: true
tags:
 - xforms
 - mark birbeck
 - sidewinder
 - ajax
 - thought piece
 - backplane
 - ubiquity
---
Determining the behaviour of an application at run-time using parameters is a
well-established practice. But whilst it's possible with command-line and
server-side applications, the scope for passing information to client-side
internet applications is limited. With the growth in internet-facing desktop
applications, widgets and gadgets, there is a need to pass parameters directly
to the application, rather than via a server, and this post looks at how that
might be achieved.

<!-- more -->

  
To illustrate, we can use the Unix command `ls` to see the contents of a
directory, and add switches like `-las` to change the behaviour:

    
      
    ls -las  
    

  
  
Similarly, on the web we can pass parameters to server-side applications,
using query strings:

    
      
    http://www.google.co.uk/search?q=ubiquity  
    

  
  
Let's look at how something similar might be done on the client.

  

# Using XPointer

  
  
We already have a mechanism to pass variables to a client, via the URI, and
that's the [XPointer Framework](http://www.w3.org/TR/xptr-framework/). I've
described before [how we use this technique in UBX
Viewer](http://webbackplane.com/mark-birbeck/blog/2007/11/passing-run-time-
parameters-to-ubx-viewer) to pass parameters that are to become `meta` and
`link` values; as UBX Viewer loads it uses these settings to configure itself.

  
But there are a number of reasons why we now need something more general
purpose, and it can easily be based on the same principles.

  

# #vars(...)

  
  
The technique we're using with the [Ubiquity
libraries](http://ubiquity.googlecode.com/) is to pass information to be used
by the client in the `vars` XPointer.

  
For example, if we have a slideshow application, and want to create a bookmark
for the third slide, we might just do it like this:

    
      
    .../slideshow.html#3  
    

  
However, if the application is also able to indicate which theme should be
used, it is better to have the capacity to set more parameters, which `vars`
gives us:

    
      
    .../slideshow.html#vars(slide=3, theme=city)  
    

  
  

# #instance(...)

  
  
Another example of how run-time information might be passed concerns XForms
applications.

  
In XForms it is possible to indicate the data to be used in the form by using
the `instance` element. The element can either hold information directly:

    
      
    <xf:instance>  
      <details xmlns="">  
        <name>Harry Angstrom</name>  
      </details>  
    </xf:instance>  
    

  
  
or via an external file:

  

    
      
    <xf:instance src="data.xml" />  
    

  
  
However, when creating a generic application the initial data will inevitably
be different for each user. In this situation we can use another XPointer,
called `instance`:

    
      
    .../createProfile.html#instance(name="Harry Angstrom")  
    

  
This has the effect of populating the default instance in the default model,
with an element called `name`, using the same rules as described in [XForms
'lazy authoring'](http://www.w3.org/TR/xforms11/#evt-modelConstruct).

  

# #src(...)

  
  
The final way that we might want to specify data at run-time is to indicate
the `@src` value of an element, so as to cause a different file to be loaded
at run-time.

  
For example, if we wanted the instance data to be loaded from a different
location, we would ensure that the instance has an `@id` value in the
document:

    
      
    <xf:instance id="p" src="" />  
    

  
  
and then specify the data source in the URL:

  

    
      
    .../createProfile.html#src(p=data.xml)  
    

  
  

# Conclusion

  
  
As the number of client-side internet applications grows there is an
increasing need to make them flexible. Passing run-time parameters via the URL
is one way this can be achieved.

