---
layout: entry
title: "The Information Resource Debate, and RDFa"
date: 2006-05-02
comments: true
tags:
 - rdfa
 - semantic web
 - thought
---
Over the weekend I looked into the whole 'information resource' question,
partly because it is pretty important for RDFa, and partly because a
discussion on it is raging on the Semantic Web Best Practices mailing list.

So I decided to go back to basic concepts and try to 'reconstruct' what
exactly it was that we were saying about documents, cars, planets and
conferences, although I have to confess that I did this in order to try to
'prove' that the whole notion of 'information resources' was flawed!

However, during this 'reconstruction' I found the opposite of what I was
expecting -- that the information resource/resource distinction is not just
useful but necessary.

<!-- more -->

I've placed my workings and conclusions into the middle of the '[Introduction to RDFa](http://skimstone.x-port.net/introduction-to-rdfa)'
document that I
had been working on. This introduction is actually more about the 'why' of
RDFa than the 'how', and is more suitable for those with at least a little
familiarity with the idea of metadata. If you want an introduction to RDFa you
might find the [RDFa Primer](http://www.w3.org/TR/xhtml-rdfa-primer/) by
myself and Ben Adida of Creative Commons more useful. And if you are
interested in implementing an RDFa processor then you can find a more detailed
look at the language itself in the [RDFa Syntax](http://www.w3.org/2001/sw/BestPractices/HTML/2005-rdfa-syntax),
by myself, Ben and Steven Pemberton.

So, if you're still here, let's press on!

## Wrong Assumptions

To begin I think it's worth flagging up some of the incorrect assumptions I
had about information resources before I started this work, since I think
seeing where I went wrong might help those who are trying to defend the
viewpoint that "a URI can't represent both a car and a web-page", but can't
for the life of them understand why the rest of us dingbats don't see it as
clearly as they do. :)

The main issue that I had a problem with was in fact the statement I just
made: "a URI can't represent both a car and a web-page". I think the problem
is that the conclusion is often presented without the 'proof' or 'workings
out', which certainly led me to think that it was flawed. The kinds of
objections I had were:

* how do you know if the URI is a web-page? Do you go and try to retrieve it?;

* why can't we talk about a web-page in the same way that we can talk about, say, a car?

The answer to these two questions actually--for me at least--gets to the heart
of the problem, and I'll refer back to them in my working outs later.

## URIs as resource identifiers and a method of retrieval

The first issue is that the mechanism for providing unique identifiers to
identify 'resources' is the URI. This is fine, and if URIs served no purpose
other than **identifying** resources there would be nothing to talk about.

However, URIs are both identifiers of 'resources' and representatives of a
mechanism that can be used to retrieve certain types of resource. Whether at
any point a web-page can **actually** be retrieved by some URI or another is
**irrelevant**, so we certainly don't need to get into things like testing the
page for a `200` or a `404`. The point is that if, in a set of statements, you
try to use a URI to represent a web document _and_ some other 'resource', you
are going to get into deep water!

Why?

Well because you are trying to represent _two_ resources with _one_ URI, and
that's not good. If you talk about resource number 1 (the web-page) and say
that it was created on Monday, how does anyone else know that you aren't
talking about resource number 2 (the car which is described on the web-page),
but was actually created last month?

So just as using the same URI to represent both London and Paris will cause
problems, so too will using the same URI to represent London and a web-page
_about_ London.

This answers my second 'confusion' in that of course we can make statements
about web-pages just like we can make statements about cars--no-one is
preventing a web-page from being seen as a resource, despite the explanations
I had seen which seemed to imply this. But because a web-page is a 'resource'
in just the same way that a car is a 'resource', we cannot use the same URI to
identify both.

## Implicit knowledge

My conclusion in the 'Introduction to RDFa' is that at the moment millions of
web-pages break this fundamental principle of not reusing the URI, although
they don't do this intentionally.

For example, if you put longitude and latitude information into the head of an
HTML document then it's generally accepted that this gives us the location of
the person who wrote the page, or maybe the company represented by the page.
Either way, the point is that we know it's _not_ giving us the location of the
page itself.

However, there is nothing in the mark-up to tell us this--we just 'know'. If
you put it in RDF terms, either:

* some automatic step has occurred to create an anonymous node that represents
  the person or company, and the location data has in turn been attached to this;
  or

* the location data has been attached to the document URI, and this therefore
  now represents both a document and a person.

In other words, processors of HTML documents that apply the location
information to objects by using 'implicit knowledge' are breaking the
information resource/resource distinction because they are actually creating
two 'resources' for the one URI.

## Silos

These processors can get away with this though, because they are running
distinct 'silos' of data; the location data about bloggers is stored in one
database because that's where I submitted my blog to, and the location data of
houses for sale is in another database since that too, is where I submitted a
link to my blog. But if you try to mix the two databases you'll get trouble,
because you'll find that the URI of my blog is being overloaded to represent
me, a person, the house for sale, and of course, a web-page. Search for
anything built after the war and you'll find me as well as a block of flats.

'Distinct'...'disconnected'...'silos'...these are not terms that we associate
with the distributed, connected, semantic web.

## Information resources as metadata resources

Note that if from the HTML you link to an external RDF/XML document to get the
geographic location data, then things are a lot easier and there won't be any
ambiguity; hopefully the RDF/XML will explicitly say that the location
information pertains to the author (or the company or the house).

This is because RDF/XML documents represent nothing other than the metadata
that they 'carry'--you can't say "this RDF/XML document was created by John on
April 1st" since RDF/XML itself is scaffolding that dissolves to leave behind
a metadata structure. You could call such documents 'metadata resources', but
this type of document doesn't _currently_ exist in the world of 'resources'.

## Recap

So to recap, various solutions around at the moment that use metadata within
an HTML document to describe things above and beyond the document itself--the
ordinary meta approach, microformats, solutions that use `@class`, etc.--are
having to use 'implied knowledge' to work out what most of the metadata means.
This solution does not scale since you constantly need to know what the
context is for your metadata (i.e., what is the collection of implied
knowledge that should be applied), and of course, it incorrectly re-uses URIs.

But the alternative solution of making the data external (_a la_ RDF/XML) is
also no good, since the whole point of RDFa is to double-up; RDFa aspires to
use web documents to carry metadata about both the document itself, and other
things. In other words, we want 'information resources' that double up as a
_describer_ of resources--or you could say, we want 'metadata resources'.
That's the reason I took the red pill over the weekend, and ventured into this
whole 'information resource' space.

## Possible solutions

There are a number of ways you could get out of this bind. The first is to
treat the URI in the same way that namespace documents are treated, and say,
"here is a URI for a car, and if you navigate to the URI given you may or may
not find some data about that car". In other words, the car is the primary
bearer of the URI, and the fact that a document can be retrieved at that URI
is just a bonus.

The rationale for this approach would be that for any given 'resource'
identified by a URI within HTTP-space there is never going to be a web-page
with the same name, because the web-page with the same name _by definition_
represents the 'resource'.

I like the logic of that, but I can't see it flying for reasons too numerous
to mention. Perhaps we could make it work if we introduced a new MIME type so
that RDFa documents join RDF/XML documents in the class of documents that sits
_outside of_ the class of XHTML documents...but that defeats pretty much the
driving goal of RDFa.

Another way out is to work out a mechanism by which URIs for 'resources' are
separate from URIs for retrieving things. So we might identify the XTech 2006
conference as:

    
    meta:http://xtech06.usefulinc.com/
    

There is no way that the web-site for the conference and this URI can clash
since they are completely different URIs. This has some problems too, but not
as many as it might appear at first sight.

But the best way of solving this problem actually turns out to be the one that
I thought was so problematic in the first place...the TAG approach whereby we
establish a convention that if we have a URI that gives us a readable,
tangible, processable document, then we should reserve that URI to represent
that document, so that we can make statements about it (when was it updated,
who wrote it, etc.). And if we reserve that URI to represent the document
itself, then other rules about 'only represent one resource with one URI' come
into play to prevent us using this URI again to represent a camera or a
holiday.

This, by the way, addresses my second 'confusion' in that the TAG approach
doesn't at all seem to be saying that we need to _test_ the URI to see if
anything comes back; we're simply establishing a convention that helps us
avoid ambiguity. (Much of what I had read seemed to say that you **must not**
use a URI that can return a web-page to represent a car or house, but the
reality is that nothing can stop you doing this, but if you do, ambiguity
arises.)

## RDFa

After working it through I can see why this solution was chosen -- it leaves the
web intact and makes things difficult for the metadata people, rather than
making it difficult for HTML authors!

Of course, that's the very reason that those of us working on RDFa had a
problem with the whole 'information resource' question in the first place,
since this seemed to now make things awkward for HTML authors as well; when we
ask them to add metadata to their documents using RDFa we now have to ask them
to come up with an extra URI for use within each document if it is 'about'
more things than just the document itself.

The '[Introduction to RDFa](http://skimstone.x-port.net/introduction-to-rdfa)'
goes into the 'workings out' and conclusions for this, and I actually think
that the resulting mark-up recommendations are not at all bad; it shows how --
in my view -- RDFa actually provides a pretty neat solution to the information
resource issue, and an easy way to provide these 'extra' URIs, when needed.

And most importantly, it shows how RDFa moves us away from the world of having
to rely on lots of pockets of _implicit_ knowledge, and instead takes an
important step towards the the more powerful world of a decentralised,
distributed, semantic web.
