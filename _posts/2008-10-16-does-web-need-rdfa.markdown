---
layout: post
title: "Does the web need RDFa?"
date: 2008-10-16
comments: true
---
After over four years at the W3C, RDFa (nee RDF/A, nee RDF/XHTML) is now a
full recommendation, which to all intents and purposes means 'it's a
standard'. Amidst the noise of the fireworks and dancing, let's have a look at
exactly what RDFa allows us to do, and why it was created.

<!-- more -->

  

## Two axis

  
  
One of the key ideas in RDFa is that a document contains two 'axis' of
information. The first is information about the document itself, such as the
fact that it has div's and span's, headers and sections, colours and font-
sizes, and so on.

  
The second set of information relates to the content of the document; a page
might be a recipe, and this section contains the ingredients. Another page
might be a user's profile, and this section contains the person's address and
phone number.

  
Information in the first category can be marked up with a standard based on a
single attribute, called @role. This allows us to indicate that some div or
span is actually playing the role of a menu or a check-box, something that is
crucial in today's Ajax-driven world. (ARIA is a standard for defined terms
like 'drop-box', 'menu', and so on, which makes use of @role. It is finding
support in IE8, FF3, and Ajax libraries such as YUI provide ARIA support,
too.)

  
Information in the second category though, is much trickier. A popular way to
spell out what some content 'means' has been to use the HTML @class attribute.
This is quite a neat solution, but the big problem is that @class has been
overused. In fact, it's been over-overused, since it bears the weight of
describing the _purpose_ of some markup (menu, footer, etc.), the _semantics_
of some markup (address, ingredient, stanza, etc.), and the behaviour of some
markup (red, black background, underlined, etc.).

  
  
@role was created to take some of the strain off @class, and to allow authors
to indicate the _purpose_ of some markup (menu, footer, etc.), and

