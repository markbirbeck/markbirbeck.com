---
layout: post
title: "Google's Social Graph API, RDFa and the future of web search"
date: 2008-02-12
comments: false
categories:
 - mark birbeck
 - rdfa
 - thought piece
---
(This article [originally appeared in February 2008](http://internet-
apps.blogspot.com/2008/02/googles-social-graph-api-rdfa-and.html) on my blog
[XForms and Internet Applications](http://internet-apps.blogspot.com/). )

  
One of the main goals of RDFa ([syntax](http://www.w3.org/TR/rdfa-syntax/) and
[primer](http://www.w3.org/2006/07/SWD/RDFa/primer/)), and its precursors, is
to provide richer semantic information for search engines. In [one of my early
drafts](http://www.w3.org/MarkUp/2004/02/xhtml-rdf.html) I gave the example of
a news story that referred to the British Prime Minister:

<!-- more -->

  
  
Yesterday in Parliament the Prime Minister said that we will

fight them on the beaches.

  
  
Such an article would be easily understood on the day of its publication, and
even years later, someone reading this would probably know who the Prime
Minister in question was, and possibly even be able to decipher 'yesterday'.

  
But what about trying to find this article? If I search in Google for articles
about Prime Ministers I'll retrieve articles about the Australian and
Malaysian PMs, both of whom are in the news this week, as well as links to the
10 Downing Street web -- site, stories about Gordon Brown, and so on. And if I
search for "Winston Churchill" the article we're using as an example won't
appear, since his name does not occur in the text.

  
So the original proposals for RDF in XHTML -- now called RDFa -- suggested
that one way to solve this problem would be to allow metadata about _anything_
to be added to HTML and XHTML documents. The example given above was extended
as follows:

  
  
Yesterday in Parliament the

Prime Minister

said that we will fight them on the beaches.

  
  
The identifier `p:WinstonChurchill` was a made up placeholder, indicating
simply that we need a unique identifier that tells us that this particular
person is Winston Churchill, but since writing that early draft,
[DBPedia](http://dbpedia.org) has come along, which means that assigning a
unique identifier for _the person Winston Churchill_ is easy.

  
So if we have the identifier, and we have a way of adding the mark-up, all we
need now is to get the data into the search engines, so that we can use them
to find the article.

  
_And [Google's Social Graph API](http://code.google.com/apis/socialgraph/)
shows that this is already possible._

  
Google have released an API that allows us to search for people and their
relationships to each other. The data is gathered during the normal indexing
process, as long as it is provided to the indexer using the [XFN
microformat](http://gmpg.org/xfn/) or via an external link to a
[FOAF](http://www.foaf-project.org/) document. This shows some interesting
developments; first, that the Google indexing process is flexible enough to
pick up different sorts of information and tie it back to a URL, and second
that the search process is flexible enough to allow users to search for these
different types of information in different contexts.

  
So the logical next step is to add an RDFa processor to the Google indexing
pipeline. Since RDFa is a generic language, able to express not just FOAF, but
_any_ RDF vocabulary, then Google will only ever need to add one RDFa
processor to their system, and that processor will allow them to index all
current and future RDFa, even if documents use vocabularies that hadn't even
been invented at the time the processor was installed.

  
This is a very different approach to the Microformats technique, since with
Microformats, each format is unique and has its own parsing rules, which would
mean that Google would have to add a different processor for each format. Not
only that, it's very difficult to make multiple Microformats in the same
document play together, and since each Microformat has to be centrally
approved, there is no great momentum being built towards formats for
disciplines such as engineering or chemistry.

  
None of which is to belittle the fact that getting XFN indexed by Google is a
great start. But whilst I've given examples here that use people -- since that
is what the Social Graph API does -- my point is that everything is in place
for something far broader; imagine that chemists could search for any document
about a particular compound or molecule (see [RDFa, chemistry and the sharing
of knowledge](http://internet-apps.blogspot.com/2006/12/rdfa-chemistry-and-
sharing-of-knowledge.html)), or that doctors were able to find all references
to a certain disease. In any number of different specialisms, experts have
already created the vocabularies that are relevant to their communities, and
RDFa has finally made it possible for these vocabularies to be used directly
in news stories, peer-reviewed papers, blogs posts and more, via HTML and
XHTML. (RDFa is in the final stages of becoming a W3C-approved specification,
but is already in use in many different environments.) [Note: RDFa is now a
full recommendation.]

  
To complete the triangle, we now need the search engines to start indexing
this data, and Google's Social Graph API initiative shows that this is finally
a technical possibility.

