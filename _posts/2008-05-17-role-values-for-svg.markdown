---
layout: entry
title: "@role values for SVG"
date: 2008-05-17
comments: true
tags:
 - xforms
 - role
---
Something which was part of the early design concepts from the [XHTML Role
Attribute Module](/standard/xhtml-role), which has got a little lost, is that
_elements_ from any language can provide a handy source of role values too.

<!-- more -->

  
To reconstruct the logic:

  
A role value is simply a URI, or resource. The reason for this is so that the
extensibility hook that we're creating puts us straight into the world of RDF.

  
Now, some values of `@role` will need to be invented. This might be because
they simply don't exist, or because we want the values to be 'cross-cutting',
and apply to many different mark-up languages.

  
But there are many values that already exist, that are suitable for use in a
variety of situations. For example, XForms has a `hint` element, that can
apply to its form controls:

    
      
    <xf:input ref="surname">  
      <xf:label>Surname:</xf:label>  
      <xf:hint>Please enter your surname or family name</xf:hint>  
    </xf:input>  
    

  
The semantics of 'XForms hint' are pretty well defined, so it should be
straightforward to apply them to other situations. For example, an Ajax
library could pick up a hint and do something with it in an (X)HTML document,
even without XForms:

    
      
    <input name="surname" />  
    <div role="xf:hint">  
      Please enter your name  
    </div>  
    

  

## SVG

  
This whole topic came up recently because someone asked whether it would be
possible to add some new values for role which would identify paragraphs,
sections, headers, and so on, and that could be used in languages like SVG;
but the answer is that if we use the XHTML `p`, `section`, `h1`, `h2`, etc.,
values then we don't need to invent new roles:

    
      
    <svg:text role="xh:h1">Metadata</svg:text>  
    <svg:text role="xh:p">  
      Metadata is data about data...which is also data...kind of  
      turtles all the way down...  
    </svg:text>  
    

  
As you can see, a role-aware voice system would be able to provide feedback to
a user in _any_ mark-up language, simply by knowing XHTML role values.

