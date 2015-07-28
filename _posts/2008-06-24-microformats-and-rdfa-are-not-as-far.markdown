---
layout: post
title: "Microformats and RDFa are not as far apart as people think"
date: 2008-06-24
comments: false
categories:
 - mark birbeck
 - rdfa
 - thought piece
---
  
The BBC have caused a bit of a storm recently by [announcing that they won't
be using the hCalendar Microformat](http://www.bbc.co.uk/blogs/radiolabs/2008/
06/removing_microformats_from_bbc.shtml) on their pages. The reason is the
[well-known problems with
accessibility](http://www.webstandards.org/2007/04/27/haccessibility/). One of
the proposed solutions is to look at [RDFa](http://rdfa.info).

<!-- more -->

  

## Confusing syntax with vocabulary

Whilst the BBC obviously understand their web-pages, and are conscious of the
issues of accessibility, they may have demonstrated here a bit of a
misunderstanding of the semantic nature of the web. And in my view, most of
the follow-up discussions I've seen fall into the same trap.

  
The main point is to not confuse the syntax used to convey information, with
the information itself--the vocabulary. The reason I think this is what is
happening is that when the BBC say "we'll be looking at the possible use of
RDFa", they may have missed the point that when you use RDFa, you still need a
vocabulary, and if one doesn't exist, you'll need to create one.

  
It would be far better to look at the Microformat that is already in use (in
this case, [hCalendar](http://microformats.org/wiki/hcalendar)), and see if it
can be tweaked to make use of the generic nature of RDFa--but that requires a
new approach to both Microformats and RDFa.

  
So before panic takes hold of the Microformats community, or smugness grips
the RDFa one, let's try to get underneath what these two technologies are
actually about.

  

## Microformats are about vocabulary

Microformats were devised as a way to let authors add little pieces of
semantics to their documents, in such a way that applications could make use
of them. An application might be a search engine that can improve indexing and
search, or a browser that can display extra information based on the embedded
values.

  
There are three key advances that Microformats made. The first was to say that
you can still do useful things, even if you only have a small amount of
information. This was pretty radical, since up until that point the semantic
web seemed to be an 'all or nothing' proposition. (For many people it still
is.)

  
The second was to say that we should be able to publish semantic information
in the same easy way that we publish web-pages--through content-management
systems, blogs, and so on.

  
But the third advance is perhaps the most important; Microformats essentially
said that we can teach end-users about a specific set of terms that help them
do something useful, without having to teach them 'big picture' stuff. This
meant that authors could be taught just enough mark-up to add contact details,
events, licensing information, geo locations...and so on.

  

## Problems with Microformats

Microformats opened the way for a new approach to the semantic web, but it
does of course have its weaknesses.

  
The first is that it 'overloads' many of the HTML attributes to carry semantic
information, in such a way that they can interfere with the normal use of the
attribute. This is why people are now having accessibility problems, because
some of the attributes are trying to play two or three roles.

  
The second weakness is that mixing vocabularies starts to get messy; because
each Microformat is a combination of vocabulary and syntax, then it is
actually quite specific, and they can therefore interfere with each other.

  
The third weakness if that because each vocabulary also requires 'syntax',
then even if a perfectly usable vocabulary exists, it has to be 'converted' to
be a Microformat.

  

## RDFa is about syntax

RDFa started life at around the same time as Microformats, and holds with many
of the same philosophies--that the semantic web is never going to happen
unless metadata is as easy to publish as a web-page, and that authors should
have an easy way to add information without having to understand the 'big
picture'.

  
But RDFa set out to address a slightly different set of problems; instead of
defining new vocabularies, it sought to create a generic syntax that can
accommodate _any_ vocabulary, allowed multiple vocabularies on a page, and did
so in _any_ mark-up language.

  
And whereas the Microformat community took as a fundamental principle that
they wouldn't modify HTML at all, we took advantage of one of the key
extension mechanisms of HTML which is that unrecognised attributes should
simply be ignored by an HTML parser (i.e., they should not throw an error). So
by adding a handful of new attributes to HTML and XHTML, RDFa doesn't
interfere with the existing uses of attributes, such as for accessibility.

  

## Microformats and RDFa

Ultimately, if people can put their prejudices to one side, there should be no
reason why these two ground-breaking technologies can't work together.

  
To illustrate, take the [rel-license Microformat](http://microformats.org/wiki
/rel-license). It's a nice, simple, self-contained document about how to use
the `license` value in `@rel` and `@rev`. To specify the license of a current
document, an author can add something like this:

    
    <a href="http://creativecommons.org/licenses/by/2.0/" rel="license>cc by 2.0</a>

This is perfectly valid HTML, making use of `@rel` with `@href`, and it's nice
and easy to explain--the key component of Microformats.

  
But it's not widely known that this is also perfectly valid RDFa.

  
However, RDFa takes this a step further, and provides authors with the ability
to talk about other things, not just the current document.

  

### Individual licenses for images

A common situation is to have a number of images on a page, and to want to
indicate the license of each of them. With RDFa you now can.

  
Imagine we have this image in our page:

    
    <img src="http://www.flickr.com/photos/detached/2529217704/" />

Our previous example of a license link needs only minor modification to make
it refer to the image:

    
    <a about="http://www.flickr.com/photos/detached/2529217704/"  
     href="http://creativecommons.org/licenses/by-nc-sa/2.0/deed.en_GB" rel="license  
    >  
      cc by 2.0  
    </a>

It's difficult to argue that this is complex, since it's only a minor change
for authors.

  

### Extending `rel-license`

But there is no reason why this formulation shouldn't also be included in
`rel-license`; after all, the key purpose of a Microformat is to promote re-
use and provide bite-size pieces that authors can quickly learn and put to
work.

  
`rel-license` could remain 'the last word' on the use of `license` in `@rel`
and `@rev`, but it could also provide information about how to use `license`
in HTML, XHTML and RDFa. That way there is only one place that authors need to
go.

  

## Solving the BBC problem

I said at the beginning that the BBC still needed to create a vocabulary, even
if it adopted RDFa. In other words, it may gain a solution to the
accessibility problem by dropping hCalendar, but it loses the advantage of
having a community-maintained syntax.

  
That doesn't mean we have the answer straight away, but I strongly suggest
that we don't throw the baby out with the bathwater here.

  
The Microformats community is responsible for getting many people to look
again at the semantic web, after having been put off by the more theoretical
approach. But it needs to build on this success, and look at updating its
formats to make use of the new attributes and generic parsing algorithm
provided by RDFa. That way it can solve its technical problems, and continue
to lead the way in defining 'agile' vocabularies for the semantic web.

  
And whilst the RDFa community has shown that it is possible to have a syntax
that supports the 'full' semantic web, without it needing to exist in a
separate space of complex mark-up and extra documents, it should remember that
a generic syntax is nothing without vocabularies.

  
I've just started work on an [exciting project to mark up job vacancies in the
UK public sector, using RDFa](/mark-birbeck/blog/2009/04/23/more-rdfa-
goodness-from-uk-government-web-sites). And although the use of RDFa will make
it very easy for departments to publish the metadata, it's still going to
require the creation of a vocabulary of terms. I'm going to be looking all
over for suitable terms and vocabularies, and I'll certainly be looking at how
Microformats might fit in.

