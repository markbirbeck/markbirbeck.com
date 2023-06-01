---
layout: post
title: "More RDFa goodness from UK government web-sites"
date: 2009-04-23
comments: true
tags:
 - mark birbeck
 - news
 - rdfa
 - ubiquity
---
  
With my semweb consultantancy hat on, I've been working for a few months now
on a number of RDFa projects with the UK's [Central Office of
Information](http://www.coi.gov.uk/). These projects have generally followed
the same pattern:

<!-- more -->

  

  * define a vocabulary for some specific area of interest, such as job vacancies or government consultations;
  

  * use that vocabulary in HTML pages, via RDFa;
  

  * get my colleagues at webBackplane to build a prototype application using Drupal and ARC2, that both publishes and consumes pages in the right format;
  

  * add an application to Yahoo!'s SearchMonkey to process the RDFa pages.
  
  
  
A couple of days ago the UK Civil Service web-site was updated with a new
look, and some exciting new features, some of which stem from the projects
I've been involved in. There is still some more testing to do, so there
haven't been any firm announcements yet, but I'm allowed to talk about one
particular feature that is very exciting, the presence of RDFa in each of the
job vacancies.

  

## Defining a vocabulary

  
  
In all of the projects I've been involved with, the first step has invariably
been to define a vocabulary. One of the most exciting aspects of the projects,
though, was that the COI were keen for these vocabularies to be both created
in public, and for the resulting vocabularies to be usable by others.

  
To facilitate this we set up an open source project called [argot-hub](http
://argot-hub.googlecode.com/), with a wiki, issue-tracking system and
associated discussion lists.

  
The first vocabularies -- or _argots_ -- that I defined were for [job
vacancies](http://code.google.com/p/argot-hub/wiki/ArgotVacancy), but in order
to make the terminology usable in other situations, I broke out argots for
[replying](http://code.google.com/p/argot-hub/wiki/ArgotReply) to the vacancy,
the specification of [contact details](http://code.google.com/p/argot-
hub/wiki/ArgotPerson), [location](http://code.google.com/p/argot-
hub/wiki/ArgotLocation) information, and so on.

  
An argot doesn't necessarily involve the creation of new terms, and in fact
most of the argots use terms from Dublin Core, FOAF and vCard. So although new
terms have been created if they are needed, the main idea behind an argot is
to collect together terms from various vocabularies that suit a particular
purpose.

  
The goal as we move forward, is to use the `argot-hub` site to host a whole
range of interconnected argots. Also, newer argots that are still being
created, are using OWL. This means that any RDFa documents that people create
can be validated, and any terminology derived can be shared.

  

## Using the argots

  
  
As the new Civil Service site took shape, the developers started to
incorporate the argots for vacancies into the vacancy searches. To see an
example of how the final result looks, first go to the [Civil Service site's
home-page](http://www.civilservice.gov.uk/):

  
![](/files/civil-service-homepage.jpg)

  
Now click in the bottom left corner on the vacancy that is listed; the jobs
will change frequently, but you'll get something that looks like this:

  
![](/files/civil-service-vacancy.jpg)

  
As you can see there is a lot of information in the page -- about the vacancy
itself (such as who to contact, closing date for applications, etc.), and
about the actual job (where it will be located, what will be involved, and so
on).

  
All of this information is not only there for humans to read, but has also
been marked up with RDFa, making it readable by other applications.

  

### Looking at the RDFa

  
  
To take a look at the RDFa, you can either do a simple _view source_ or you
can run the document through an RDFa parser and get the output as a set of
triples.

  
If you look at the source you'll see something like this:

  
![](/files/civil-service-vacancy-source.jpg)

  
There are [a number of RDFa parsers around,](http://rdfa.info/rdfa-
implementations/) if you want to see what the triples look like. Our own
parser -- [backplanejs](http://backplanejs.googlecode.com/) -- can do all
sorts of things with the data, one of which is to show the resulting triples
as a simple tree-view, to aid debugging the RDFa in the page. You can add a
link to the parser as a bookmarklet, as described in [Getting started with
RDFa: Creating a basic FOAF profile](/mark-birbeck/blog/2009/04/getting-
started-with-rdfa).

  
If you do use the backplanejs parser via a bookmarklet, then after you click
on the link, you should see a list of all of the subjects in the document, as
a tree view:

  
![](/files/civil-service-vacancy-checkrdfa.jpg)

  
The first and last subjects are just part of the checker itself, so ignore
those. Also, the second subject is the document itself, which will contain
triples, but not ones that relate to the vacancy and the job. It's the third
and fourth subjects that we're interested in.

  
Expanding the third subject gives information about the vacancy:

  
![](/files/civil-service-vacancy-checkrdfa-vacancy.jpg)

  
We can see that the vacancy is a FOAF document that was published by the Civil
Service, has a title, identifier, description and abstract, and is made up of
another piece of information, linked to via the Dublin Core `hasParts`
property.

  
The 'other part' of the vacancy is the actual job that the vacancy is
advertising, and we can see those triples by expanding the fourth subject:

  
![](/files/civil-service-vacancy-checkrdfa-job.jpg)

  
These triples tell us the job salary, the location that an employee would be
working in, whether travel is required, and so on.

  

## Why is the Civil Service using RDFa?

  
  
We'll have to wait for the full site announcement to hear _exactly_ why the
Civil Service chose to add RDFa to its pages. But there are a few things that
we can deduce.

  
(That's me saying that this is all my opinion.)

  
The first is that by using RDFa to mark-up vacancies on each individual
government site, it's possible to allow each department to publish jobs
however it sees fit. Many companies want to have some centralised information,
not just government, but this usually involves imposing on each department
some new database system or workflow. By using RDFa as the interface, each
department merely needs to have the ability to publish HTML, and then they can
share their data.

  
The second is that by publishing vacancies using RDFa, it's easy for _third-
parties_ to 'scrape' the data into their own databases, in a reliable way.

  
For example, some external company could import all vacancies for a particular
region or of a particular type, and then show them on their own site. And of
course, since Yahoo!'s SearchMonkey can process RDFa, then all vacancies can
be viewed through Yahoo!'s search engine:

  
![](/files/searchmonkey-fco.jpg)

  

## Conclusion

  
  
RDFa is rapidly proving its usefulness, and a number of UK government projects
are leading the way in showing how RDFa can be used, and deployed.

