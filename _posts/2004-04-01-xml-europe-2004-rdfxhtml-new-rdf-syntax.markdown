---
layout: post
title: "XML Europe 2004: RDF/XHTML: A new RDF syntax"
date: 2004-04-01
comments: false
categories:
 - event
 - rdfa
---
The first public presentation of RDFa.

![](/files/xml-2004-logo.gif)

This presentation by Mark Birbeck was given at [XML Europe 2004](http://web.archive.org/web/20071026191737/http://idealliance.org/papers/dx_xmle04/).

(RDF/XHTML was eventually renamed RDFa.)

<!-- more -->

Presentation title: RDF/XHTML: A New RDF Syntax ([link](http://web.archive.org/web/20071026104449/http://idealliance.org/papers/dx_xmle04/html/abstract/04-04-02.html))

By: [Mark Birbeck](/mark-birbeck)

Presentation abstract: We have two standards running parallel with each other;
(X)HTML is the de facto standard for document markup, accounting for millions
of items on the web. RDF is a standard for expressing metadata, which in turn
provides a foundation for making use of that metadata, such as reasoning about
it. Yet the former is very rarely the subject of the latter; meta information
placed in the HTML family of documents is often encoded in such a way as to
make it difficult to extract by RDF-related parsers. And if it cannot be
extracted, then it cannot be used.

RDF is about statements and triples. There are a number of syntaxes which can
be used to express these triples, such as N3 and RDF/XML. However, there are a
number of problems with incorporating any of these syntaxes into XHTML,
including validation issues, ease of authoring and therefore HTML community
acceptance, and browser support.

This paper describes a new (1 March) meta-data module for XHTML2 that makes it
easy for processors to extract metadata as RDF triples, but without putting an
unnecessary burden on authors familiar with HTML.

While RDF/XHTML has been specifically designed for use in XHTML2, it is easy
to apply to any XML markup.
