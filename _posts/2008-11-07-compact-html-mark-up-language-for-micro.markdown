---
layout: entry
title: "Compact HTML: A mark-up language for micro-blogging"
date: 2008-11-07
comments: true
tags:
 - mark birbeck
 - rdfa
 - thought piece
---
When sending small comments via services such as Twitter, it's pretty
straightforward to add links to other documents. The general pattern is to
abbreviate the link using an online service, and then paste the shortened link
into your post. Software that displays your posts can then replace any string
that begins with `http:` with a real link.

<!-- more -->

  
However, there are many occasions where a link is just not good enough.
Sometimes you'd like to embed an image, or even a video. But if we start
trying to add HTML mark-up, we'll pretty soon hit the character limit imposed
by micro-blogging platforms.

  
Enter compact HTML, or CHTML...for short.

  

# Compact HTML

  
  
The simple idea of CHTML is that we use keywords to indicate the mark-up. For
example, to add an image you would ordinarily write:

    
      
    <img src="http://www.fineart.ac.uk/images/works/Dundee/90/du0008.jpg" />  
    

  
Which would give you this:

  
![](http://www.fineart.ac.uk/images/works/Dundee/90/du0008.jpg)

  
Of course, we can shorten the fragment by using a URL service:

    
      
    <img src="http://snurl.com/252rj" />  
    

  
  
But we can go further if we make the mark-up compact:

    
      
    img=http://snurl.com/252rj  
    

  
  
That's a pretty efficient way to transmit an image in a post.

  

# Why HTML?

  
  
You could ask how this relates to HTML?

  
The idea is that all elements and attributes from HTML can be used in a
generic way. So the full version of the image we just saw, would actually be:

  

    
      
    img(src=http://snurl.com/252rj)  
    

  
  
We could also write:

  

    
      
    img(src=http://snurl.com/252rj,alt=A picture of Stooky Bill)  
    

  
  
Each element would have a 'default attribute' that anonymous values would set.
In the case of `img` it would obviously be `@src`, so

  

    
      
    img=http://snurl.com/252rj  
    

  
  
is equivalent to:

  

    
      
    img(src=http://snurl.com/252rj)  
    

  
  

# Other tokens

  
  
Since all we're really doing is looking for patterns of the form:

    
      
    _token_=_value_  
    

  
  
then we needn't limit ourselves to HTML in our compact mark-up.

  
For example, we could express a YouTube video like this:

  

    
      
    tube=http://snurl.com/25300  
    

  
  

# An example

  
  
To see this in action, take a look at the [Compact HTML Twitter sample](http
://ubiquity-rdfa.googlecode.com/svn/tags/0.7.2/_samples/compact-html.html)
from the [Ubiquity RDFa library](http://ubiquity-rdfa.googlecode.com/). Note
how two tweets from Stooky Bill are shown, one containing an image, and one
containing a video. As you can see from [Stooky's Twitter
page](http://twitter.com/stookybill), the actual tweets contain simple Compact
HTML.

