---
layout: post
title: "RDFj: Semantic objects in JSON"
date: 2009-04-20
comments: false
categories:
 - mark birbeck
 - rdfa
 - thought piece
 - ubiquity
---
One of the features of our RDFa parser ([Ubiquity RDFa](http://ubiquity-
rdfa.googlecode.com/)) is the ability to import RDFa from external documents.
This is particularly useful for bringing in definitions for templates and
other processing rules that you would like to have applied to the document
being parsed, or for importing one definition into another (`owl:imports` is
implemented this way). The technique used to garner these triples is simply to
import the external document into a hidden `iframe`, and then run the parser
on it. However, as the JavaScript programmers amongst you will know, that only
gets you so far; if the document containing the RDFa you want to import comes
from a different domain to the one that your source document originates from,
most browsers won't give you access to the DOM in the `iframe`. There are all
sorts of ways to try to work around this, and a common one is to use a server
to convert the RDFa to JSON, since `script` tags aren't victims of the cross-
domain limitation. We therefore decided to create a JSON format that was as
close as possible to RDFa. Of course, since RDFa itself is a serialisation of
RDF, then really we were actually looking to create a JSON format for RDF.

<!-- more -->

## RDF/JSON

A JSON format for RDF does already exist, called
[RDF/JSON](http://n2.talis.com/wiki/RDF_JSON_Specification), and devised by
some of the clever semantic web folks at [Talis](http://talis.com/). However,
the main problem with this serialisation is that it's sole purpose is to be
just that -- a serialisation. Although it 'does what it says on the tin', it
doesn't do any more than that.

## JSON and semantic objects

We found that our goal was quite different. We've been using JavaScript for
more and more of our work, and the RDFa parser was increasingly becoming a
dynamic JSON object creation mechanism (more on this in a future post). We
therefore wanted a closer relationship between JSON and RDF than RDF/JSON
provided.

### Functions as objects

For example, we often retrieve a set of triples from a triple store, use the
triples to create JSON objects, and then check to see if any of the objects
have 'action' properties on them. If we find such a property, it is executed
as a function. Since the `action` property will serve as a function, the most
natural way to express this in JSON would be as follows:  { "http://ubiquity-
rdfa.googlecode.com/action": function(obj) { doSomething(); return; } }  Of
course, we could have expressed this in RDF/JSON by turning the function into
a string and then having our code use `eval()` to execute it:  { "_:abc" : {
"http://ubiquity-rdfa.googlecode.com/action" : [ { "value" : " \
doSomething(); \ return;", "type" : "literal" } ] } }  However, there are many
benefits to passing around 'real' functions rather than strings. An obvious
one is not having to use the JavaScript line continuation character all the
time, but probably the main benefit is that we'll spot any errors in our
script before the code is executed; if our editor supports syntax highlighting
we'll see errors there, and when our code is loaded in the browser, the
function will be parsed then, and any JSON loading errors will show up.

### Anonymous objects

Another example of how we'd like to take advantage of the natural flow of JSON
is with anonymous objects. It's common to have structures where one object
relates to another, and in RDF this is often achieved with a bnode --
essentially an identifier that is only usable as a reference within the
context of the document, and not by any other documents. RDF/JSON would
represent the relationship between such structures like this:  {
"http://example.org/about" : { "http://purl.org/dc/elements/1.1/title" : [ {
"value" : "Anna's Homepage", "type" : "literal", "lang" : "en" } ] ,
"http://xmlns.com/foaf/0.1/maker" : [ { "value" : "_:person", "type" : "bnode"
} ] } , "_:person" : { "http://xmlns.com/foaf/0.1/homepage" : [ { "value" :
"http://example.org/about", "type" : "uri" } ] ,
"http://xmlns.com/foaf/0.1/name" : [ { "value" : "Anna Wilder", "type" :
"literal" } ] } }  Here we are saying that the web-site at URL
`http://example.org/about` was 'made' by the item that has the bnode
identifier `_:person`, and in turn, that the item with the bnode identifier
`_:person` has the name 'Anna Wilder'. RDFj supports the same kind of
'explicit bnode' approach as well:  [ { "$": "<http://example.org/about>",
"http://purl.org/dc/elements/1.1/title": "Anna's Homepage",
"http://xmlns.com/foaf/0.1/maker": "<bnode:person>" }, { "$":
"<bnode:person>", "http://xmlns.com/foaf/0.1/name": "Anna Wilder",
"http://xmlns.com/foaf/0.1/homepage": "<http://example.org/about>" } ]
However, in situations where an item is not referred to from elsewhere, RDFj
also provides a more natural format -- the JSON object being referred to
becomes the actual value of the predicate:  { "$":
"<http://example.org/about>", "http://purl.org/dc/elements/1.1/title": "Anna's
Homepage", "http://xmlns.com/foaf/0.1/maker": {
"http://xmlns.com/foaf/0.1/name": "Anna Wilder",
"http://xmlns.com/foaf/0.1/homepage": "<http://example.org/about>" } }

## From JSON to RDF, rather than RDF to JSON

Essentially, what we've done with RDFj is to map JSON to RDF -- with a few
extra tweaks thrown in -- rather than simply mapping RDF to JSON. (RDFa took
the same approach, starting with HTML, and then working out what RDF various
patterns might represent.) There are of course many uses for the
straightforward serialisation approach, taken by RDF/JSON. But we're finding
that as our applications increasingly use _both_ JavaScript and RDF, it's very
useful to blur the lines between the two. RDFj takes us an important step
towards that. The [latest definition of RDFj](http://code.google.com/p
/ubiquity-rdfa/wiki/Rdfj) is on the [Ubiquity RDFa Google Code project](http
://ubiquity-rdfa.googlecode.com/).

