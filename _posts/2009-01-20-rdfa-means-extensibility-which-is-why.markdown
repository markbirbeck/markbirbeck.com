---
layout: post
title: "RDFa means extensibility (which is why some people will never support it)"
date: 2009-01-20
comments: true
tags:
 - mark birbeck
 - rdfa
 - role
---
In this post I look at how RDFa and other techniques to add extra information
to documents evolved around the same time as Microformats, as part of the work
at the W3C on the next generation of web languages. Whilst the traditional
approach taken to language design at the W3C -- and now used in HTML5 -- seeks
to anticipate authors' every needs, the RDFa approach resulted from a focus on
clearly defined _extension points_ that give _authors_ control. In this post I
look at the different approaches taken to markup languages, as a way to
explain the problems `@role` and RDFa solve, and conclude that although HTML5
is unlikely to shake its monolithic structure (and will therefore not support
extension mechanisms like RDFa), RDFa itself will continue to flourish.

<!-- more -->

### Microformats

[Microformats](http://microformats.org/) was a great development. But despite
the way that Microformats has allowed people to do some neat things at the
nexus of web-page/browser/desktop, they've never realised their full potential
because of some fundamental flaws. The key problem is that to create a
vocabulary requires something akin to a full standards-process, and standards-
making has proved over the years, to be laborious, time-consuming, and often
acrimonious. It's therefore no accident that there remain very few
Microformats.

### Extensible

Microformats began its life at much the same time that the XHTML 2 Working
Group (at the time called the HTML Working Group) began rethinking the way
that `@class`, `meta` and `link` should work in XHTML. The key design decision
taken at that time was that we needed a markup language that was _extensible_.
Unfortunately, over the years the notion of extensibility has been reduced to
the blunt instrument of schema combination; using tools such as XML Schema or
RELAX NG, language creators could bolt other languages together by combining
and adding to their schemas. But anyone who has tried this will know that it
is something of a black art, and it rarely happens that anyone but a
specialist would add any extensions to a language.

### Content adaptation

However, alongside this notion of extensibility another one has been gathering
momentum; that the language should _itself_ contain extension points that
authors and organisations can use, without having to go back to standards
bodies. A good illustration of the need for extensibility came back in 2004. I
was presenting some of the 'new ideas' from XHTML 2 at a W3C workshop on
content adaptation, and was struck by the fact that in two of the other
papers, companies had effectively invented their own markup language by adding
well-defined `@class` values, and a few new elements and attributes. Barry
Haynes and Matthew Clough presented the 'Orange Markup Language', or OWL,
whilst Dan Appelquist from Vodafone talked on VCML; both languages aimed at
adding information to documents to:

  * help the reformat process when displaying on different devices;
  * determine which parts could be moved to menus and which parts needed to remain in the main window;
  * add semantics, so that, for example, a football score in column two of a table wouldn't get separated from the team names in column one;
  * specify themes;
  * and more.
It's worth looking at the presentations (see [W3C Workshop on Metadata for
Content Adaptation](http://www.w3.org/2004/06/DI-MCA-WS/) for links to all the
papers), just to realise the problems that organisations like mobile phone
operators, news publishers, aggregators and so on are trying to solve. But
from a standards point of view, the most interesting point about the two
'languages' was that they were completely different. It meant that if Reuters
were to supply documents of news stories to both Orange _and_ Vodafone, it
would need to provide two separate feeds, each using the correct language.

## Extension Points

The 'extensible' aspect of the new work at the W3C at that time was about
trying to address these requirements, but not by constantly adding new
language features -- instead we were looking at providing _extension points_
to a base language. There are two kinds of extension points that organisations
likes Orange and Vodafone were in need of. The first was to able to describe
aspects of the document itself, and the second was to be able to describe the
_content_ of the document.

### Describing the document itself

When we talk about the document itself we need to be able to identify that
some parts are menus, others are footers, and yet another segment is the main
content. Armed with this basic information we can do some interesting things.
For example, just knowing which `div`, `p` or `section` contains the main
content greatly helps those who are using screen-readers; they could skip past
menus, headers and adverts and get straight to reading the content. Similarly,
knowing which parts of a document are the menus means that mobile operators
could move all of that content to the shortcut buttons on the phone, and leave
more room on the display to show the main content. In the past, indicating
which elements played which role would have been achieved through the use of
the `class` attribute. The problem was that this had been used to hold all
sorts of values, and there was no guarantee that a value of `menu` in one
organisation would have the same meaning in another. So we proposed a new
attribute called `role`, which is specifically for describing the _purpose_ of
an element (see [XHTML Role Attribute Module](http://www.w3.org/TR/xhtml-
role/), as well as [Using the role attribute to extend XHTML](http://internet-
apps.blogspot.com/2006/08/using-role-attribute-to-extend-xhtml.html) and [The
XHTML role attribute: small and perfectly formed](http://internet-
apps.blogspot.com/2006/07/xhtml-role-attribute-small-and.html)). Not only
that, this new attribute could contain identifiers that were unique across the
web, which meant that we could really be sure that we had 'a menu', or 'a
footer', because the identifier had been constructed to indicate exactly that.
The `role` attribute was quickly taken up by the [WAI-
ARIA](http://www.w3.org/WAI/intro/aria) work, which defined a set of common
identifiers, and the `role` attribute is now implemented in Firefox (see the
[Mozilla Accessibility Project](http://www.mozilla.org/access/)) and IE 8 (see
[What's New for Accessibility in Internet Explorer
8](http://msdn.microsoft.com/en-us/library/cc304059(VS.85).aspx)).

#### Elements and attributes

Some would say that there is no difference between adding a footer using an
element:

    
    <footer>
      webBackplane.com (C) 2008 webBackplane, a trading name of Backplane Ltd.
    </footer>

and adding one using a `role` value:

    
    <div role="footer">
      webBackplane.com (C) 2008 webBackplane, a trading name of Backplane Ltd.
    </div>

but there are some important differences. The first relates to the standards
process that I've mentioned; adding a `footer` element to a language takes
time, and requires jumping through all sorts of hoops. In the end it's those
with the time and energy to devote to the standards process itself whose
voices are heard, usually from companies who can afford to have someone spend
a lot of time on the topic. But adding a footer _role value_ takes no time at
all, and you don't need to ask anyone. Of course, if you want to create some
`role` values that other people will use, perhaps in your industry or field of
learning, then you'll need to co-ordinate with them. But even so, that's a far
cry from having to appeal to the W3C to add your favourite element or value to
their language. So whether you are chemists or TV guide publishers, you can
determine you own set of values, within your own timeframe, and promote them
however you want, without having to wait for the wheels of the standards
process to turn. I would just add that there is another difference between
elements and attributes and that concerns the semantics; with the element
technique we are saying we have 'a footer', which in many situations is fine.
But with the attribute technique we are saying we have 'a `div` that is
playing the role of a footer', which is slightly different. It means that the
_structural_ aspect of what we have is preserved -- it might be a `div`, but
it could also be a `span` or a `p` -- and we are merely augmenting that. To
some extent this is a matter of taste, so there's no point in trying to find
principles here. A lot will depend on what else is going on in your document.
For example, if you have a jQuery query that finds all `div`s with a `@class`
value of _panel_, and sets them so that they are hidden until a user requests
to see them, then using `@role="footer"` instead of `footer` plays nicely with
that. Or if you have many roles attached to elements in a document (menus,
main content, footers, headers, supporting content, sidebars, and so on), then
you might find it much easier to read as a collection of sections with role
values, then a collection of specifically-named elements.

### Describing the document's content

The second area of extensibility that many people need concerns marking up the
document's _content_. Once again the traditional solution was to use the
`class` attribute to hold information such as 'this is an address', or 'this
is a location'. Microformats formalised some of these uses. But the problem
here is that 'this is a ...' only gets you so far, and you quickly want to add
properties about the item; its longitude and latitude, or its Creative Commons
license (see [RelLicense](http://wiki.creativecommons.org/RelLicense)), for
example. Microformats tries to get round this by using existing HTML
attributes to hold these values, but you soon run out of attributes to use
(not to mention that the 'meaning' of an attribute often has to be bent a long
way to make it fit its new use). The _extensibility_-based solution to this
problem was to devise a new set of attributes. However, unlike the `role`
attribute, these new attributes were specifically for describing _content_.

#### RDFa

The set of attributes became formalised as RDFa (see [RDFa
Primer](http://www.w3.org/TR/xhtml-rdfa-primer/) and [RDFa in XHTML: Syntax
and Processing](http://www.w3.org/TR/rdfa-syntax/)), allowing authors and
organisations to add extra information about their documents' content. And as
with `@role` there is no need to wait for the terms and values that can go
into the attributes to be passed down from on high; instead you can use your
own terms, whether that's in your organisation, across all mobile phone
operators, within all hospitals, [amongst research chemists](http://internet-
apps.blogspot.com/2006/12/rdfa-chemistry-and-sharing-of-knowledge.html), and
so on.

### Why object to extensibility?

Whilst I've explained the goals of some of the design decisions for `@role`
and RDFa, it's clear that not everyone likes the 'extension points' approach.
The [WHATWG](http://www.whatwg.org/) for example are pursuing a much more
monolithic approach with [HTML5](http://www.w3.org/html/wg/html5/); they see
no need for extension points, since the language itself will cover everything.
But this means that if your company or organisation doesn't have the resources
to allocate someone to be involved in the standards process, your perfectly
reasonable requirement will go unsupported. The Microformats approach is also
counter to the idea of 'extension points' that are open to anyone, since it,
too, attempts to centrally control the creation of new formats, stifling the
evolution of new vocabularies by specialists within their sectors. Which
means, unfortunately, that HTML5 is unlikely to add support for RDFa, since
it's completely counter to the aims and goals for HTML5. But lest anyone gets
too worried about that, bear in mind the following:

  * RDFa is being parsed from HTML pages by [Yahoo!'s SearchMonkey](http://developer.yahoo.com/searchmonkey/);
  * RDFa is being promoted by Creative Commons as the approved way of adding licensing information;
  * the London Gazette has all of its pages marked up using RDFa (see [SemWebbing the London Gazette](http://2008.xtech.org/public/schedule/detail/528)), making it the first major RDFa project in the world;
  * RDFa is central to a couple of projects I am working on for the UK government's Central Office of Information;
  * [RDFa is being incorporated into Drupal core](http://groups.drupal.org/node/16597).
The prospects for being able to extend documents without having recourse to
standards organisations is enormous, and consequently interest is growing fast
in RDFa. We should therefore concentrate for now on its use for people who
need extensibility points in their documents and applications.

