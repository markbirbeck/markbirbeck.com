---
layout: entry
title: "Duck-typing and XForms"
date: 2009-02-03
comments: true
tags:
 - xforms
 - mark birbeck
 - role
---
In a recent code review on the [Ubiquity XForms](http://ubiquity-
xforms.googlecode.com/) project, the question of whether to test for an
element by name or properties came up. In this post we look at the benefits
that can be had from using _duck-typing_ as a way to manage objects'
functionality, rather than the more usual hierarchical solutions.

<!-- more -->

  

## OO hierarchies

  
  
The usual sort of example given when looking at and explaining object
hierarchies, is to say that we have a class called `Vehicle`, from which we
can derive a class `Car` and `Bicycle`.

  
What I've always hated about this kind of ordering is that you actually spend
more time discussing where to put properties than actually writing code. For
example, is a motorbike a type of bicycle, that just happens to have an
engine? Is a van a small lorry?

  
And more to the point, who cares?

  
Well, the Department for Transport might care, but they also might change
their definitions based on all sorts of criteria, such as lead in petrol,
engine size, number of wheel axles, and so on. In fact, if you were in charge
of building the DfT a system, then you would certainly be asking for trouble
if you encoded their definitions of vehicles directly into the object
hierarchy of your application.

  

## Duck-typing

  
  
Many programmers will be familiar with the idea of [duck-
typing](http://en.wikipedia.org/wiki/Duck_typing), which is a different way to
look at objects in object-oriented systems.

  
The name comes from the idea that 'if something walks like a duck, and quacks
like a duck, then it's a duck'. So if an object has a property of `wheels` and
perhaps `engine`, then it's a `Vehicle`.

  
This can make for very efficient systems, because the 'type' of an object is
no longer tied directly to its class in the source code, which as we know is
very difficult to change, but is instead something that can be determined at
run time. More than that, the 'type' of an object is worked out at the point
of need, allowing objects to take on many personae, in a way that multiple
inheritance is simply not able to cope with. For example, our `Vehicle`
becomes an object of type 'printable', merely by the presence of a `print()`
method.

  

## XForms

  
  
The XForms language wasn't designed with duck-typing in mind, but as I'll
illustrate, duck-typing is an incredibly powerful way to implement a flexible
and extensible XForms processor.

  
We'll use the XForms `hint` element to illustrate.

  

### The hint element

  
  
The XForms hint element contains text or mark-up that will be displayed to a
user when the hint event is fired, which is usually related to the `mouseover`
event in browser-based processors. When the event occurs, the relevant text or
mark-up is displayed to the user, and when some other event occurs (such as a
`mouseout`, a timer expiring, etc.) then the hint is hidden.

  
The actual text to be displayed can be obtained inline from the element, or
via the usual binding techniques, such as using `@ref`, `@value` or `@bind`.

  
So we have two aspects to a hint; the first concerns its behaviour -- and you
could say this is the key to its _hintyness_ -- whilst the second aspect
concerns what should be displayed.

  
The second aspect -- the 'what to display part' -- is actually common to a
number of elements in XForms, such as `xf:output`, `xf:help`, `xf:alert`, and
so on; they can all either display inline text, or text that is obtained from
the instance data.

  
In the UXF library, the two `aspects` of a hint are kept separate, on two
separate objects, and these two objects are dynamically added to a hint object
at run time. This is different to a traditional object model where we would
have a hint object that inherits from the 'what to display object', creating a
rigid hierarchy; in our model we have a _hintyness_ object that handles the
'show and hide' part, and we have a 'what to display object' that manages the
data, and we bring the two together when we encounter the XForms hint element.

  
The beauty of this is its flexibility; we can 'attach' our 'what to display'
object to anything, independent of whether it's a hint, and the object will
manage the processing of `@ref` and `@value`. And we can also attach our
'hinty' object to anything to make it behave like a hint, independent of the
'what to display' behaviour; SVG circles or HTML5 video objects could all be
given hint behaviour, simply by attaching the _hintyness_ object.

  

### The role attribute

  
  
The obvious way to indicate in mark-up that we have an object of a certain
type is to use the element directly. For example, in XForms we could write:

  

    
      
    <xf:hint>Please enter your name</xf:hint>  
    

  
  
or:

  

    
      
    <xf:hint ref="msg" />  
    

  
  
But the [`role` attribute](/standard/xhtml-role) opens up the possibility of
assigning functionality to non-XForms elements, for example:

  

    
      
    <span role="xf:hint">Please enter your name</span>  
    

  
  
Now we get our duck-typing in reverse; 'if an element says that it wants to be
a duck, give it the _waddle_ and _quack_ methods'.

  

## Ubiquity XForms

  
  
The Ubiquity library makes use of this dynamic binding technique to create
flexible run-time objects, through the use of a _decoration_ mechanism; a set
of rules have been created that indicate which objects to attach to which
elements, and when a document loads, the functionality is 'wired in' to each
element, as expressed in the rules. Adding new elements is easy, since it just
requires adding more rules.

  
But the code review I mentioned earlier related to the other side of this, the
original duck-typing question. We had a piece of code that needed to toggle a
`xf:case`, and the natural inclination was to check the name of the element,
to ensure we really had a _casey_ element, i.e., one that could be toggled.

  
Yet to be an element that could be toggled only requires the presence of the
`toggle()` method; just as anything that quacks is a duck, so anything that
can be toggled is a case.

  
So instead of checking the element name:

  

    
      
    if (NamespaceManager.compareFullName(oCase, "case", "http://www.w3.org/2002/xforms")) {  
      ...  
    }  
    

  
  
our code now checks for the method:

  

    
      
    if (oCase && typeof oCase['toggle'] === 'function') {  
      oCase.toggle();  
    }  
    

  
  

## Loosely-coupled Objects

  
  
Another key design goal of the Ubiquity suite is that the objects that make up
the system are 'loosely-coupled'. This means that they can change
independently of each other, and as a consequence, improvements in one area
should immediately benefit other areas.

  
In this case, imagine that a rule is added to the decorator module such that
the presence of `@role="xf:case"` on a `div` causes all _caseyness_
functionality to be attached. With the duck-typing way of testing whether to
toggle, the following mark-up will just immediately start working:

    
      
    <xf:switch>  
      <xf:case id="error">...</xf:case>  
      
      <div id="ok" role="xf:case">...</div>  
    </xf:switch>  
    

  
With the prior method -- checking the name of the element -- it would not have
worked until we had added code to check for the presence of `@role`. In other
words, we would have been in exactly the same situation as a normal object
hierarchy application, adding special code to test for specific things.

  

## Conclusion

  
  
Whilst duck-typing can be confusing if used in every aspect of an application,
it can work well when its boundaries are clear. In Ubiquity XForms and
[Ubiquity SMIL](http://ubiquity-smil.googlecode.com/), elements can acquire
rich combinations of functionality based on their position in a document, the
element name, the presence of other attributes, and so on. In this situation,
duck-typing is a natural fit, and keeps the architecture open.

