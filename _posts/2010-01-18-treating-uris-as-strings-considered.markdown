---
layout: post
title: "Treating URIs as strings considered dangerous"
date: 2010-01-18
comments: true
tags:
 - xforms
 - mark birbeck
 - thought piece
---
Since URIs are often conveyed as strings it's tempting to manipulate them as
such, but it's better--and safer--to delegate URI manipulation to special
functions. These can then have their own unit-tests, which will take into
account the edge-cases that can catch us out.

<!-- more -->

  
I've just been doing a quick code review in the Ubiquity XForms project, and
one thing caught my eye that I thought might be worth a post.

  

## Forms submission

  
  
In forms submission -- both in XForms and HTML forms -- we often need to add
parameters to a URI.

  
For example, if we have the URI `http://example.org`, and the parameters `a=b`
and `c=d`, then the resulting URI should be:

  
http://example.org?a=b&c;=d

  
It seems pretty straightforward that we need to add the parameters to the URI,
with a '?' in between:

  
[uri] + '?' + [parameters]

  
  

## Adding parameters

  
  
We can see that the parameters themselves have been added by taking the name
and value (`a=b`, etc.), and adding it to the URI, ensuring that for all
parameters other than the first, there is a separator between. (The separator
can be either an '&' or a ';'.)

  

## Base URIs already containing parameters

  
  
The bug that needed fixing though, was that this 'naive' concatenation doesn't
work if the URI you are dealing with already contains parameters.

  
For example, if we have the URI `http://example.org?x=y`, and we need to add
the same two parameters we had before, then our simple concatenation would
give us:

  
http://example.org?x=y?a=b&c;=d

  
when what we actually need is:

  
http://example.org?x=y&a;=b&c;=d

  
As you can see, if we already have a '?' then we don't need to add another, so
it seems that a simple addition to our concatenation code would be to use a
call to `indexOf` to see if there is a '?' present, and only add another if we
don't find one.

  
There's a small additional test we'll need to make which is to check whether
the last character in the URI is a '?', as I'll explain.

  

## Base URIs with empty query strings

  
  
Recall that we added the parameters by placing `a=b` and `c=d` onto the end of
the URI, separated by '&':

  
http://example.org?a=b&c;=d

  
Now, if we already have a URI with a query then we need to ensure that there
is an additional '&' placed before our first parameter:

  
http://example.org?x=y&a;=b&c;=d

  
But what if the base URI has a query indicator (i.e., the '?') but no
parameters? In other words, what if we have this URI:

  
http://example.org?

  
In this situation we _don't_ want to add the extra separator, otherwise we'll
get this:

  
http://example.org?&a;=b&c;=d

  
So our rules now become that we only want to precede our parameters with a
separator if the '?' is not the last character in the URI. It's a little
awkward, but thanks to `lastIndexOf`, I'm sure we can manage.

  

## Base URIs already containing fragments

  
  
However, there's a further subtlety; what if the URI contains a fragment
identifier?

  
For example, if we have the URI `http://example.org#x`, and we need to add the
same two parameters we had before, then our simple concatenation would give us
this:

  
http://example.org#x?a=b&c;=d

  
The fragment identifier part of the URI has now become `x?a=b&c;=d` because
it's always the last part of the URI. What we actually want is to insert the
new parameters _before_ the '#':

  
http://example.org?a=b&c;=d#x

  
Now we need to add another use of `indexOf` to check for a '#', and if we find
one, use its position as the point at which to insert the parameters.

  

## Context is everything

  
  
However, the assumption behind using `indexOf` and `lastIndexOf` in this way
is that a URI will contain only one '?'. A secondary assumption here is that
the only time you'll ever see a '? is as an indicator of the query part of the
URI.

  
Both of these assumptions are incorrect.

  

### Question marks in parameters

  
  
The first assumption is that you can only have one '?' in a URI. However, [the
query section of RFC
3986](http://labs.apache.org/webarch/uri/rfc/rfc3986.html#query) explicitly
flags up that the '?' character is a valid parameter value. For example, we
can have `a=finished?` as a parameter.

  
This means it's quite easy to envisage scenarios where there is more than one
'?' in the URI:

  
http://example.org?a=finished?&c;=d

  
This won't necessarily mess up our first use of `indexOf`, but it will mess up
the use of `lastIndexOf` as a way to check whether you need to add an extra
separator. Recall that we wanted to avoid turning this:

  
http://example.org?

  
into this:

  
http://example.org?&a;=b&c;=d

  
so we used `lastIndexOf` to check whether the last character in the URI was a
'?'. But that algorithm will turn this:

  
http://example.org?a=finished?

  
into this:

  
http://example.org?a=finished?c=d

  
You might need to look closely to spot it, but because the last character was
a '?', we haven't added a separator before the `c`, and as a result, instead
of having two parameters (`a=finished?` and `c=d`) we have only one
(`a=finished?c=d`).

  

### Question marks in fragments

  
  
The interesting thing about the previous examples is that at least you know
you have a query string, so you might be tempted to still use `indexOf` to
manipulate things. After all, although we may have too many '?' characters, we
still know that we have query.

  
However, with the [the fragment section of RFC
3986](http://labs.apache.org/webarch/uri/rfc/rfc3986.html#fragment) all bets
are off; here we can see that '?' is explicitly allowed as a fragment
character.

  
This means that it's possible to have a '?' in a URI _even if it doesn't have
a query_. For example:

  
http://example.org#finished?

  
This may seem like a contrived example, but actually it's not, for two
reasons.

  
The first is that the fragment part of a URI is carefully defined to allow
anything, because we don't know how it will be interpreted. You may think that
"finished?" is not valid as an HTTP fragment, but what about in the scheme
"xyz"?

  
And this is the key point; since HTML forms and XForms can ultimately deal
with any scheme, since that's how the web is designed, we must write our
algorithms defensively, and not assume anything.

  

## Safe URI handling

  
  
Hopefully this delving into some of the subtleties of URI handling and parsing
--and we haven't even begun to talk about turning relative paths into absolute
paths, handling encoded characters, and so on-- has done enough to convince
you that you shouldn't manipulate URIs directly, as simple strings.

  
The only way to be completely sure of what is happening is to use special
functions to unpack a URI into its various components, then manipulate those
components--perhaps adding additional parameters to the list of query
parameters, but it might also be to turn a relative path into an absolute path
--before finally reassembling the URI.

  
This may sound like a lot of work, but it's the only way to be sure that
characters don't get incorrectly interpreted as a consequence of their
position in the URI not being taken into account.

  
In the [backplanejs library](http://backplanejs.googlecode.com/) these
functions are in the URI module. (This is also imported into the [Ubiquity
XForms library](http://ubiquity-xforms.googlecode.com/).) Breaking up a URI
simply involves calling `spliturl`, which returns an object containing all of
the parts. For example:

  
spliturl( "http://example.org?a=finished?" )

  
would give us the object:

  
{

scheme: "http:,

authority: "example.org",

path: ""

query: "a=finished?"

fragment: ""

}

  
It's then an easy matter to manipulate the query part, before creating a new
URI with the `recomposeURI` method.

  

## Conclusion

  
  
Since URIs are often conveyed as strings then it's tempting to manipulate them
as such. But the problem with doing this means that the context of a character
is rarely taken into account when processing.

  
It's better--and safer--to delegate URI manipulation to special functions.
These can then have their own unit-tests, which will take into account the
edge-cases that can catch us out.

