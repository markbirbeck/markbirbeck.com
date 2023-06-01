---
layout: post
title: "Using JavaScript's replace function to stem the spread of eval()"
date: 2008-10-17
comments: true
---
One of the recurring patterns in the [Ubiquity RDFa library](http://ubiquity-
rdfa.googlecode.com/) is the need to substitute the property of an object into
a string of text. The general pattern is that templates such as a tooltip, or
piece of script, should have all occurrences of any variables replaced with
the actual value of those variables from a run-time JavaScript object.

<!-- more -->

  
The following simple template illustrates the idea, using longitude and
latitude values to build a URL for a Google Map image:

    
      
    <img src="http://maps.google.com/staticmap?markers=${lat},${long}&zoom=8&size=150x100&key=_site key_" />  
    

  
In this template we would want `${long}` to be replaced with `obj["long"]`,
and `${lat}` to be replaced with `obj["lat"]`.

  

# The `eval()` hack

  
  
When I first started working on the code a few years ago I used naive `eval`s
to do this. The first step was to replace each occurrence of `${...}` with a
string concatenation that contained code to extract the `obj` value. The
substitution would be `' + obj["long"] + '`, for example:

    
      
    <img src="http://maps.google.com/staticmap?markers=' + obj["lat"] + ', + obj["long"] + '&zoom=8&size=150x100&key=_site key_" />  
    

  
Then all that was needed was to push this new string -- with apostrophes added
at the beginning and end to match the opening and closing ones added by the
code -- through `eval()` and the result would be a string with live values
substituted in.

  
Of course, we all know that `eval()` is evil, and it was always the intention
to replace this with something better.

  

# The hack starts to get flakey

  
  
As is often the way, the hack began to take on a life of its own, because I
was increasingly finding templates that would cause it problems, and which
then needed to be worked around.

  
For example, I had to ensure that I didn't use apostrophes in the templates,
and that wasn't always possible. So the code soon got an update that would
escape all apostrophes:

  

    
      
    format.tooltip.content.replace.replace(/\'/g, "\\\'")  
    

  
  
But things are starting to get a little flakey when we have to do things like
this, and the code soon became littered with constructs like this:

    
      
    if (format.tooltip) {  
      var t;  
      try {  
        eval(  
          "t = '" +  
          format.tooltip.content.replace(/\n/g, "").replace(/\'/g, "\\\'").replace(  
            /\$(?:\{|\%7B)(.*?)(?:\}|\%7D)/g,  
            "' + obj.$1 + '"  
          ) +  
          "';"  
        );  
      } catch(e) {  
        t = "error: " + e.description;  
      }  
    

  
  
It was time to remove `eval()`.

  

# Removing `eval()`

  
  
Eventually I found time to address this. My plan was to use a facility of
`replace()` whereby a function can be called for each matching string, giving
the code the opportunity to provide a replacement value.

  
The regular expression to find the tokens is the same as it was, but the
difference is that the second parameter to `replace()` is not a string but a
function:

    
      
      t = format.embedTemplate.content.replace(  
        /\$(?:\{|\%7B)(.*?)(?:\}|\%7D)/g,  
        function (m) { return getPropertyFromVar(obj, m); }  
    );  
    

  
The function is passed the matching token, which in the example would be
"${long}" and "${lat}", so we still need to extract the property name:

    
      
    function getPropertyFromVar(obj, m, errors) {  
      var ret;  
      var props = m.match(/\$(?:\{|%7B)(.*?)(?:\}|%7D)/);  
    

  
If there is a result from this regular expression then an array will be
created:

    
      
      if ( props ) {  
    

  
The value we want will be in the second position in the array, and in the case
of the example above, this string will on one occasion be "long", and the
second time through will be "lat". The string can be used directly to find the
value of the relevant property from the object passed in:

    
      
        ret = (obj[ props[1] ])  
          ? ((obj[ props[1] ].content) ? obj[ props[1] ].content : obj[ props[1] ])  
          : ((errors) ? "No value for '" + props[1] + "' (" + m + ")" : "");  
      } else {  
        ret = (errors) ? "No key in '" + m + "'" : "";  
      }  
      return ret;  
    }  
    

  
  
As you can see, with a separate function to handle the token substitution it
becomes much easier to handle errors, such as those that arise from requesting
a value that doesn't exist.

  
But perhaps most importantly, the dangerous spread of the evil `eval()` has
been stalled, at least for another day.

