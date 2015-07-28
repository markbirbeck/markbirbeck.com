---
layout: post
title: "Getting started with RDFa: Creating a basic FOAF profile"
date: 2009-04-14
comments: false
categories:
 - mark birbeck
 - rdfa
 - tutorial
 - ubiquity
---
Just over a year ago I wrote a blog post that showed how to create a FOAF
profile on a web page, using RDFa. The idea was not only to show how easy it
was to do in terms of the markup, but also to illustrate that once you are
able to publish RDF via a web page, you need nothing more than a blog page to
join the semantic web. This blog post updates that [old post](http://internet-
apps.blogspot.com/2008/02/first-steps-in-rdfa-creating-foaf.html), by first
adding some guidance on how to check your document (using the Ubiquity RDFa
parser), and then proceeding to add more features to your blog page.

<!-- more -->

## Introduction

Now that the RDFa syntax is a full standard, and organisations like Yahoo! and
Google are starting to index the data (see [Google announces support for
RDFa](http://rdfa.info/2009/05/12/google-announces-support-for-rdfa/) and
[Yahoo! into semantic web](http://rdfa.info/2008/03/14/yahoo-into-semantic-
web/)), it's worth putting more of your own data into your web-pages, by way
of RDFa. A simple place to start is to modify your home-page or blog profile
so that it includes FOAF information.

## FOAF

If you're not familiar with [FOAF](http://www.foaf-project.org/), or Friend-
of-a-friend, it's a set of terms that can be used to describe people and
organisations, as well as their relationships to each other. For example, we
can mark up our names, point to our home-pages, indicate the companies and
projects we work on (and point to _their_ home-pages), and so on. As this
vocabulary continues to gain in popularity, and since RDFa allows us to use
any vocabulary we like without having to re-write it (or ask anyone), we'll
use FOAF via RDFa to mark up our pages. We won't use every part of the
vocabulary in this tutorial, so if you want to find further properties, or
more detail on the properties used below, look at the [full FOAF
specification](http://xmlns.com/foaf/spec/). Before we create our page, let's
look at some steps we can take to ensure our data is correct.

## Validating our data

One of the problems with creating RDFa in your documents is that you don't
know how it is going to look to a machine. Of course, many people don't get
their HTML or CSS right either, but at least they can view their pages in a
web-browser, and see if the desired effect is achieved. RDFa is less forgiving
though, not because it's more difficult (is anything more difficult than
getting CSS right?), but because it needs to be precise. For example, if I
want my profile to say that I am the Managing Director of Backplane Ltd., I
need to get that the right way round -- to say that Backplane Ltd. is the
Managing Director of _me_ would be nonsensical.

### The Ubiquity RDFa parser

The [Ubiquity JavaScript library](http://ubiquity.googlecode.com) includes
modules for XForms, SMIL and RDFa. The parser can be used in other libraries,
by importing one or more of its store, parser or query modules. An
[Owl](http://en.wikipedia.org/wiki/Web_Ontology_Language)-based validator is
being worked on, but even before this is ready, the parser can be used to
check our documents simply by 'dumping' the metadata it discovers in a page.
Then, if we get any unexpected metadata, we know that something is up.

### Validating using a bookmarklet

There are a number of ways to check your RDFa documents, but the model we'll
discuss here involves checking your pages by loading them into a browser, and
then invoking a bookmarklet which loads the Ubiquity RDFa parser. The parser
supports the use of 'formatters' to determine the display of any metadata it
finds, and the checker uses a simple tree view formatter to show its results.
To obtain the checker bookmarklet, simply navigate to the [checker installer
page](http://ubiquity-rdfa.googlecode.com/svn/trunk/install-checker.html) and
follow the instructions there. Once you have the checker installed, we'll
begin creating our FOAF profile.

## Creating a person

The first thing we'll do is to create a person object that will hold our
information. This is done using the RDFa `typeof` attribute, which is much
like `@class` in HTML. The type of the object we want to add is a `Person` and
since 'person' comes from the FOAF vocabulary, we write it like this:

...

If you are adding your profile to a blog and you have control over the
template, then you can simply add the `foaf` prefix mapping to the `html`
element as we did above. However, if you are using a system that you don't
have control over, then you'll probably want to create a containing element
onto which you'll place all of the prefix mappings that you'll need.

### Checking your work

Once you've saved your changes, simply click on the "Check RDFa" bookmarklet,
and after a short pause you should see a list of the subjects in your
document. There should be an entry that begins `bnode:` and if you expand it,
you should see an entry that says:  <http://www.w3.org/1999/02/22-rdf-syntax-
ns#type> <http://xmlns.com/foaf/0.1/Person>  Note that there will be other
metadata in the list, some of which relates to your document, and some of
which is used by the checker itself. Check that you get _exactly_ what is
referred to here, because if you don't, other systems won't understand your
data. For example, did you use an upper-case 'P' for 'Person'? Did you define
the `foaf` prefix mapping? Once this is correct, we're ready to add our
personal information to this block of 'type' person.

## Adding personal information

The FOAF vocabulary is packed with useful properties that we can set, so let's
start with some basics such as our name and the URL for our blog. We can add
our name using the `foaf:name` property, which is set via the RDFa `property`
attribute:

Mark Birbeck

Our blog is indicated using the `foaf:weblog` property. However, unlike
`foaf:name` which is simply a string of text, the item we're going to refer to
is a URL, so we must use the HTML `rel` attribute instead of `@property`:

Mark Birbeck [XForms and Internet Applications](http://internet-
apps.blogspot.com/)

If we save this, refresh the page, and click on the checker button again, then
under the same `bnode:` entry we looked at before, we should see entries that
look something like the following:  <http://www.w3.org/1999/02/22-rdf-syntax-
ns#type> <http://xmlns.com/foaf/0.1/Person> <http://xmlns.com/foaf/0.1/name>
"Mark Birbeck" <http://xmlns.com/foaf/0.1/weblog> <http://internet-
apps.blogspot.com/>

## Adding friends and colleagues

Now that we have our basic framework in place, it's pretty easy to drop in
more and more FOAF properties. Perhaps the most commonly used is `foaf:knows`
which is used to indicate the people that you know. Since the target of this
property is once again a URL, we'll need to use the HTML `rel` attribute
again:

Mark Birbeck [XForms and Internet Applications](http://internet-
apps.blogspot.com/) [Ivan Herman](http://www.w3.org/People/Ivan/#me)

Note the way that we are able to refer to Ivan's FOAF information by using the
URL that ends with `#me`; this is a useful convention, and easily achieved in
our own profile by adding `@about` to our containing element:

...

## Adding a picture

The FOAF vocabulary also allows us to indicate pictures that we appear in, and
pictures that we might want others to use to represent us. To set a picture of
yourself that other software might use, use the `foaf:img` property:

Mark Birbeck [XForms and Internet Applications](http://internet-
apps.blogspot.com/) [Ivan Herman](http://www.w3.org/People/Ivan/#me) ![Picture
of Mark Birbeck](http://www.formsplayer.com/files/pictures/picture-11.jpg)

## Linking to a Twitter account

The final illustration we'll show is how to use the FOAF vocabulary to point
to your Twitter account, which will make it easy to build tools that will
allow people to follow you with one click. The first thing to do is create a
relationship called `foaf:holdsAccount`, which will connect our 'person
object' with an online account object. To connect two objects we use the HTML
`rel` attribute again:

Mark Birbeck [XForms and Internet Applications](http://internet-
apps.blogspot.com/) [Ivan Herman](http://www.w3.org/People/Ivan/#me) ![Picture
of Mark Birbeck](http://www.formsplayer.com/files/pictures/picture-11.jpg) ...

Next we create an object of type `foaf:OnlineAccount`, in exactly the same way
that we did when creating a _person_ earlier:  ...  Finally, we indicate that
the particular type of account we're dealing with is a Twitter account (using
`foaf:accountServiceHomepage`), and also provide our account name (using
`foaf:accountName`):  [Twitter](http://twitter.com/) markbirbeck

## Human and machine-readable

Everything we've marked up so far is human and machine readable, but the
layout is not great for a human. Although the links to the blog will work, and
the text will show names and accounts correctly, there is no context
information. However, additional mark-up can be placed in the document, and as
long as it is outside of the scope of the RDFa attributes it won't be classed
as metadata. For example:

Mark Birbeck writes a blog called [XForms and Internet Applications](http
://internet-apps.blogspot.com/). He knows [Ivan
Herman](http://www.w3.org/People/Ivan/#me).  ![Picture of Mark
Birbeck](http://www.formsplayer.com/files/pictures/picture-11.jpg) His inane
comments are available on his  [Twitter](http://twitter.com/) account. His ID
is ' markbirbeck '.

## The whole shebang

If you want to use the mark-up as a template, then here is everything that
we've seen, above. Just replace my values with your own details, add any
human-readable text you want around it, and you are off and running. Don't
forget to keep using the checker, as you go:

Mark Birbeck [XForms and Internet Applications](http://internet-
apps.blogspot.com/) [Ivan Herman](http://www.w3.org/People/Ivan/#me) ![Picture
of Mark Birbeck](http://www.formsplayer.com/files/pictures/picture-11.jpg)
[Twitter](http://twitter.com/) markbirbeck

## Publishing your FOAF page

Since our FOAF page is embedded into an HTML page, then you can publish your
FOAF profile pretty much anywhere that you are able to publish HTML or XHTML.
My FOAF information is on [my profile page](/profile/mark-birbeck). You can
see all of the metadata by using the checker.

