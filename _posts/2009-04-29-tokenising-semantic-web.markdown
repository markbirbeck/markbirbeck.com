---
layout: post
title: "Tokenising the semantic web"
date: 2009-04-29
comments: false
tags:
 - rdfa
 - thought piece
---
This particular thought piece is aimed at people who are familiar with aspects
of the semantic web. You don't need to be an expert to understand the article,
and you might find it interesting, even if you know next to nothing about the
semantic web. But I'm putting this little disclaimer right at the top here, so
that no-one can say, "there you go...I told you the semantic web was difficult
to understand, and Birbeck's latest blog-post just proves it".

<!-- more -->

# GPS

Myself, I have no idea how GPS works -- I just press the buttons, and I either
arrive at my destination, in awe of the technology, or I complain when I'm
directed to follow a road that doesn't actually exist. But just because I want
GPS to be easy to use, doesn't mean that the underlying technology has to be
easy to comprehend. There's nothing simple about getting a satellite or two
into space, for example. Likewise the maths involved in the Doppler Effect --
in fact, even the way that the Doppler Effect is used to help us to locate
ourselves -- is not easy. So the people who work on improving GPS for the rest
of us need a space in which to talk to each other without constantly being
told that trilateration is too difficult for the average user. This piece is a
little bit like that; its intent is to spark a discussion amongst the men and
women in white coats who are trying to make the semantic web easier for
everyone else to _use_. One day the technology behind the semantic web might
also be easier to _understand_ -- but that's not today's problem. So let's get
started.

# Unique Identifiers

A fundamental component of the semantic web is the URI. A different way of
saying this is that a central requirement of being able to manage human
knowledge is having the ability to uniquely identify things. We could use any
scheme to identify the things that humans want to store information about, but
it just so happens that through an interesting sequence of events relating to
the internet, the most popular and enduring has been the URI.

# Identifying terms

To illustrate; when it comes to talking about something, we often have the
notion of its _title_. It might be the title of a book or an album, but it
could also be the title of a chapter within the book or a track within the
album. The idea of a _title_ might also reasonably apply to jobs that people
do, vacancies advertised to fill those jobs, and magazines used to carry the
adverts to fill the vacancies. Now, if we want to store this kind of
information in our computer systems in such a way that we can reuse it in
interesting ways, then it makes sense to know when we are using this concept
of _title_. The _de facto_ way to do this is to mint a URI, and the most
commonly used URI for the notion of 'a title' was created by the Dublin Core:
![The Dublin Core 'title' URI](/files/tokenise-dc-title.png) If we all agree
to use this identifier in our data when we want to talk about the _title_ of
something, then when we move data from one application to another, or request
data using web services, we should have no trouble knowing what the data
means, and therefore what we can do with it.

# Vocabularies

URIs for terms like _title_ often come as a package. For example, if you're in
the business of providing a title for a book, then you probably also want to
indicate who wrote it, what the nature of the book's copyright is, when the
book was published, who by, and so on. The [Dublin Core Metadata
Initiative](http://dublincore.org/) is an example of a collection of these
terms, often called a _vocabulary_: ![Dublin Core terms with common URI
part](/files/tokenise-dc-uris.png) As you can see, such a collection of terms
-- and here we're only showing a handful from the full Dublin Core list -- has
a common part to the URI of each item. In a sense, it's the URI of the
vocabulary _itself_ which acquires significance -- in this case
_http://purl.org/dc/terms/_ -- and all of the other pieces of information are
items that occur within that vocabulary: ![Vocabulary terms have a common URI
base](/files/tokenise-dc-vocabulary.png)

# Abbreviating URIs

The 'common part' we just saw -- the vocabulary URI -- is often swapped out
for a prefix to make things easier to write. All that is needed is some way to
indicate that a prefix maps to some vocabulary URI. For example, we might use
the prefix 'dc' to refer to the Dublin Core vocabulary: ![Vocabulary terms
have a common URI base](/files/tokenise-dc-prefix-mapping.png) We can now
reuse this prefix to stand in for the vocabulary URI, when using terms from
the vocabulary: ![Vocabulary terms have a common URI base](/files/tokenise-dc-
prefix-use.png) This is certainly a labour-saving device, and when used with
multiple vocabularies at the same time, can make data more readable. When
using XML-based languages, the mapping of a prefix to a vocabulary can be
easily achieved using XML namespace prefixes. For example, the following
snippet of RDFa uses XML namespace prefixes to make terms from both the Dublin
Core and FOAF vocabularies available:  ![Mapping prefixes for DC and
FOAF](/files/tokenise-dc-and-foaf-prefixes.png) Once the mappings have been
established -- i.e., the namespace mappings declared -- they can be used in
mark-up to abbreviate the URIs:

#  Dr Ivan Herman

## CURIEs

The technique used in this RDFa example to specify terms from a vocabulary, is
defined in the document [CURIE Syntax 1.0](http://www.w3.org/TR/curie/). The
approach is simply that a value like _foaf:name_ -- which is called a CURIE --
is processed in the following way:

  1. the part _before_ the colon is extracted (_foaf_);
  2. the corresponding URI is found, by locating the XML namespace designated using the prefix (_http://xmlns.com/foaf/0.1/_);
  3. the part _after_ the colon (_name_) is then appended to this URI, to give a full URI (_http://xmlns.com/foaf/0.1/name_).
Applying this process to each of the CURIEs in the example above would give
the following mappings: ![Full URIs using prefixes for DC and FOAF](/files
/tokenise-dc-and-foaf-in-use.png) As you can see, these steps have the effect
of recreating the original URIs.

# Tokens for vocabularies

So let's recap a moment. We know that we need URIs to identify things,
otherwise our dumb computers will have no idea what we're talking about. But
we also know that URIs are too long and unwieldy, so we want to abbreviate
them. Using a substitution prefix like `dc:` or `foaf:` and the technique
defined in the CURIE syntax specification is a convenient way to shorten URIs.
But we have to recognise that we haven't actually mapped tokens to URIs here.
Instead, we've created a token to represent the _vocabulary_, and then
appended terms from the vocabulary to it. It's still a useful technique, but
we'll see in a moment why it's nowhere near as powerful as it could be. To
help us understand how we could do better, we need to take a little diversion
into Microformats.

# Microformats

The Microformats technique allows authors to embed tokens into their mark-up
which can be spotted by software, and used to make some actions available to
the end user. A collection of tokens is called a microformat. For example, an
author may use the collection of tokens in the [hCard
microformat](http://microformats.org/wiki/hcard), to provide information about
a user, like so (example taken from the specification):

[Tantek Celik](http://tantek.com/)

This piece of mark-up indicates a person's full name ("Tantek Celik"), and the
URL of their web-site ("http://tantek.com/"). It's pretty straightforward for
software to scan a document looking for these tokens, and once located, to
make the data available in some way. For example, Tantek's details could be
easily added to your contact list, passed to your mobile phone via Bluetooth,
hooked up to Skype, and so on.

## Limitations

To those involved in the semantic web, the limitations to Microformats are
well known. The main problem is that it is not always clear where the matching
piece of information can be found, once a triggering token has been located.
In the example above, there is no _generic_ rule that says 'whenever you find
an element with a class of _url_, take the associated `@href` value as the
URL'. Similarly, there is no generic rule that says 'whenever you find an
element with a class of _fn_, take the associated inner text of the element as
the full-name value'. Instead, a parser needs to know the intricacies of the
hCard format, and must look for the specific settings. Such a parser can only
be used on one microformat, and there is nothing that can be generalised for
use when parsing other microformats. The other criticism that is often
levelled at Microformats is that by using unqualified tokens, the collection
of formats cannot be scaled for use in the semantic web. This is because it is
not possible to tell which vocabulary should be used when values like 'fn' are
encountered (although the presence of _root class names_ like 'vcard' do go
some way to reducing this problem). The usual response from semantic web
enthusiasts is to favour the use of qualified terms over tokens -- as we saw
in the earlier sections, with the use of CURIES -- so that the vocabulary
being used is unambiguous. But I'd like to break with this convention and show
that the token approach used in Microformats should be regarded as a strength.

## Strengths

Whilst it's obviously true that having unqualified values like 'fn' and 'url'
make it difficult to bring Microformats into the semantic web, we should be
careful not to throw the baby out with the bathwater; what may be a weakness
in terms of scalability, is a _strength_ when it comes to authoring documents.
Authors need only use simple values in their documents, without having to get
involved with XML namespaces or other forms of prefix mappings. Of course, at
some point our dumb machines still need to know how to map the token, but it's
a lot better to get the machines to do the work, and allow authors the freedom
of using simple tokens.

# Tokens for URIs

So the problem we need to solve is to make it possible to create tokens that
are easy for authors to use, but at the same time ensure that the mechanism
used to create the tokens is scalable. We discussed above how the most widely
used technique for shortening URIs at the moment is to create a prefix, which
can then be used to represent vocabularies, from which terms can be used:
![Full URIs using prefixes for DC and FOAF](/files/tokenise-dc-and-foaf-in-
use.png) This technique fits our second requirement of being scalable, since
the prefixes ensure there is no chance that a term in one vocabulary could be
confused with a term in another, even if the terms have the same name. But
this extra power doesn't really address our goal of making authoring easier;
yes, it gives us the ability to disambiguate terms across the entire internet,
and that is important -- but the way it achieves this becomes overkill when
authoring individual documents, where there is almost never confusion about
the use of terms. The simple proposal then is that rather than tokens being
limited to representing _vocabularies_, they can represent an _entire URI_. In
fact, this is already possible; a token already maps to a full URI, as we saw
above when creating prefixes for the Dublin Core and FOAF vocabularies:
![Mapping prefixes for DC and FOAF](/files/tokenise-dc-and-foaf-prefixes.png)
But the problem is that these tokens are then used as prefixes for other
values, which must then be combined to create the full URI: ![Full URIs using
prefixes for DC and FOAF](/files/tokenise-dc-and-foaf-in-use.png) To make
things easier for authors we should allow the first step of mapping a token to
a full URI to be the _only_ step: ![Full URIs expressed using tokens](/files
/tokenise-dc-and-foaf-tokens.PNG) We can now look at how we might carry out
this mapping.

## Arcane knowledge

One way to provide the mapping from a token to a full URI is to simply build
it into whatever language and associated parser needs it. This is the approach
we took with RDFa, as I'll explain. The definition of the CURIE syntax
_already allows_ for predefined tokens to be mapped directly to URIs. The idea
is that any language using CURIEs can provide a list of tokens that map to
full URIs, and these should be processed before any other CURIE processing
takes place. In fact, if the tokens are 'known' then no further processing
needs to happen. This technique is used to good effect in the RDFa
specification, where tokens such as _license_ and _next_ are defined to map to
a full URI in the XHTML namespace. This means that authors are able to use
mark-up such as this:  or this:  ![](/a-photo.jpg) to indicate which license
applies to their document or photographs, and they don't need to express any
namespaces or provide a prefix. Instead, the RDFa parser simply 'knows' about
the mapping from a token such as _license_, to the full URI
(_http://www.w3.org/1999/xhtml/vocab#license_). And although it wasn't
designed this way, a similar example of using arcane knowledge to map a token
to a URI can be found in SPARQL; the token _a_ can be used to represent the
full URI _http://www.w3.org/1999/02/22-rdf-syntax-ns#type_ (see [SPARQL Query
Language for RDF, section 4.2.4 rdf:type](http://www.w3.org/TR/rdf-sparql-
query/#abbrevRdfType)). Once again we have a very convenient mapping, but once
again a processor would need to have arcane knowledge of this mapping.

## Extending CURIEs

Whilst relying on arcane knowledge gets us so far, as a solution it will
ultimately suffer from the same problem as Microformats -- an inability to
scale. Of course, some people will argue that this is not an issue for RDFa,
since the main motivation for introducing the token technique into CURIEs was
to allow the commonly used HTML/XHTML values like _next_ and _prev_ to map to
meaningful RDF -- and that problem was solved. (The alternative would have
been to insist that authors write their mark-up like this:  which would
probably have killed RDFa in the water.) But whatever the original motivations
for the CURIE-token technique, it provides us with an interesting way to
achieve our goal of making RDFa documents easier to author. A simple
modification to the CURIE processing rules would be to say that if a token is
the same as a prefix declaration, then that URI should be used. This would
still allow languages such as XHTML+RDFa or SPARQL to define their own tokens
through 'arcane knowledge'. But most importantly, it would allow authors to
define their own tokens, too, and open up the possibility of collections of
common tokens to be created.

### Problems with Using Namespace Declarations

Returning to our earlier RDFa example, it would seem straightforward to recast
it like this:

#  Dr Ivan Herman

As you can see, the declaration of a mapping from a token to a URI is achieved
in exactly the way it was before, by using namespace declarations, but in use
the token is completely substituted by the URI, rather than forming part of
the URI as it does in current practice. A key advantage though, is that the
author is now free to choose the token that maps to the URI. For example, in
the mark-up we have been using, the _foaf:name_ property
(_http://xmlns.com/foaf/0.1/name_) is represented by the token _name_, but it
could just as easily have been represented by the token _fn_, so as to form an
association with the name of the property in hCard and vCard:

#  Dr Ivan Herman

Similarly the _foaf:homepage_ property (_http://xmlns.com/foaf/0.1/homepage_)
could be represented by the token _url_, whilst the _foaf:Person_ type
(_http://xmlns.com/foaf/0.1/Person_) is represented by the token _vcard_, both
of which are used in hCard:

#  Dr Ivan Herman

[Ivan Herman's homepage](http://www.w3.org/People/Ivan/)

Note that this is not intended to be understood by a Microformats hCard
parser, since it is not using the hCard syntax. The idea is simply to provide
a mechanism by which tokens can be used to express full URIs, so as to ease
the authoring of documents, and the sharing of semantic formats.

# Specifying token mappings

We've seen how the XML namespace mechanism can be used to specify tokens in
the document, but there are two major problems with this approach; the first
is that it mixes mappings from the document's 'infrastructure' with mappings
about the documents content, when in real use, there is rarely a coincidence
of the two. And the second issue is that due to the hierarchical nature of
namespaces, it is impossible to import a set of mappings into a document from
some external source, and so share tokens.

## Token mappings inline

To illustrate the first problem, take a property like _foaf:name_; we know
that such a property could be used in an SVG document, an XHTML document, an
HTML5 document, a XAML document, and so on. The namespaces used to create the
SVG, XHTML, HTML5, and XAML documents are unlikely to appear as statements in
the metadata, whilst the FOAF vocabulary is even less likely to appear on an
element or attribute in the mark-up. In other words, there is no architectural
need for the prefix mapping technique used in the RDFa itself to be the same
as the prefix mapping technique used for defining the host document. The CURIE
specification already allows host languages the freedom to determine how
prefixes should be mapped, so here we propose a new attribute called `@token`
to contain a list of token mappings. The exact syntax of `@token` doesn't
matter (for the moment) so much as the use to which it can be put; it might
look something like this:

#  Dr Ivan Herman

[Ivan Herman's homepage](http://www.w3.org/People/Ivan/)

or this:

#  Dr Ivan Herman

[Ivan Herman's homepage](http://www.w3.org/People/Ivan/)

The key point though, is that the token definition for use in document
semantics, is now completely independent of namespaces used in document
structure.

## Packaging up token mappings

If all we did was to use `@token` instead of `@xmlns` then things might be
easier to understand, but we wouldn't be able to do anything more than we can
today. The problem with namespaces is that they only apply to the element they
are attached to, and all of its children, which means that there is no way to
add namespaces in one part of a document that will apply to another part --
and that, in turn, means there is no way to import prefix mappings, using
namespaces. However, HTML already provides a way to indicate that a particular
set of tokens is being used, by way of the `@profile` attribute. The only
problem is that there is no clear definition as to what format the document
referred to by `@profile` should take. (See [the profile
attribute](http://www.w3.org/TR/html401/struct/global.html#adef-profile) in
the HTML 4.01 spec.) From an RDFa point of view the easiest technique would be
for `@profile` to refer to another RDFa document. For example, we could create
a simple RDFa document that contains nothing other than our tokens:  and then
make use of this in our first example, as follows:

#  Dr Ivan Herman

[Ivan Herman's homepage](http://www.w3.org/People/Ivan/)

It's easy to see how files of commonly used tokens for particular purposes can
be created and shared. And it's also possible to imagine going much further,
and for tokens to be derived from a SKOS or OWL document, expressed in RDFa
and referred to using `@profile`. This is something we'll return to in a
future post.

# Summary

To summarise the argument; first, we know that we need to be able to tokenise
URIs, because they tend to be long and unwieldy. However, whilst the current
use of prefixes to abbreviate URIs is useful, it only allows us to tokenise
URIs that are used to identify vocabularies, and not full URIs. It's also not
ideal that these prefixes are being expressed using a document architecture
mechanism -- `@xmlns`. By adding a new attribute -- `@token` -- to the _CURIE
processing_ rules, we can tokenise full URIs, which gives authors the same
level of simplicity that Microformats has. The key difference though, is that
this tokenisation is completely scaleable, and so overcomes one of the major
drawbacks of Microformats.

