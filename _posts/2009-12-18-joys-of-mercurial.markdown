---
layout: entry
title: "The Joys of Mercurial"
date: 2009-12-18
comments: true
---
No-one needs to be told that any project you work on needs a version-control
system. Nowadays, even when writing documents or OWL ontologies, I'll use
source control, and if it's something that can be shared with others, then I
might even start up a new Google Code project, so that I have a wiki and
issue-tracking, too.

<!-- more -->
  
Up until a few months ago, my VCS of choice was SVN, and it served us well.
But more recently I decided to give a distributed version-control system a
try, and to my surprise found not only something that could track my work, but
something that facilitated a new way of working.

  

## Choosing a DVCS

  
  
The two main contenders seemed to be Mercurial and Git, although there are
definitely others. As far as I can tell, Git is the more advanced of the two,
and I was about to start using it when I discovered that Google Code supported
Mercurial. Since the differences between the two are not that great, and since
I've always been of the opinion that time spent trying to choose the 'best' is
would be better spent getting on and learning, I plumped for Mercurial.

  
But I'd like to stress that even if Git and Mercurial were worlds apart, it
wouldn't make much difference, because the significant difference between
these two and SVN is that it makes for a completely difference workflow.

  

## Source-control as backup, and the problem of code reviews

  
  
For many years I've seen VCS as essentially a kind of 'back up'. It's a way of
storing the code that matches a particular release of your software, so that
you can go back to it if a customer spots a bug. It's also a central location
that is authoritative for your code-base.

  
But over the last year or so, I've found that this model doesn't really help
when it comes to managing code that is contributed to a project. If you allow
many programmers to commit to the project, then the history becomes a mish-
mash of comments about fixed bugs, branches, code reviews, and so on.

  
For example, a common problem is that inexperienced programmers will commit
too much code at a time, for review. Contributions will invariably include a
mixture of basic tidying up -- such as switching tabs and spaces, removing
erroneous comments, or ensuring that code conforms to a house style -- some
bug fixes that were spotted 'whilst I was there', and then of course the
proper fix itself.

  
Of course, more experienced programmers will split these elements into
separate commits, so that reviewers can focus on just what has changed. But
with a tool like SVN, this is actually quite difficult to manage.

  
For example, imagine that you start work on fixing issue 123. You add your
tests, add some code, and things are going quite well. You discover some old
comments that are no longer relevant, and delete them. You see that some of
the code doesn't conform to the house-style, and you fix that. And then as
often happens, you realise that to fix issue 123, you actually need to add
support for something else that no-one had thought of before; you dive into
that, and add your tests.

  
Ideally you should submit code for two reviews now; the whitespace and coding
style changes can usually go straight into trunk, but the new feature and the
fix for issue 123 should be submitted for review.

  
But how to separate them?

  

## Mercurial as a patch-manager

  
  
The answer is of course, creating a set of patches, and getting those
reviewed, but although 'mastering patches' has been on my long list of things
to do for quite a while now, I'd never quite got round to it.

  
So it came as quite a surprise to discover that a key feature of Mercurial is
its ability to manage patches -- this moves version control out of the
category of a backup system, and into a crucial part of the programmer's
toolbox. Programmers can now 'craft' their patches for submission, grouping
different pieces of work together in one patch, and then submitting them for
review. Rather than fighting against the VCS, the system helps them to get
their code ready.

  
In Mercurial for example, I can use `hg qrecord` to interactively create three
patches from the mixture of changes that I described a moment ago. Or if the
changes are in separate files, I can create patches that only contain certain
files, by using the `-X` and `-I` options on `hg qnew`.

  
But once you know what you are doing with patches, it really becomes even
easier, as I'll illustrate.

  
My favourite way to work now is to create a basic housekeeping patch at the
beginning:

  
  
hg qnew housekeeping

  
  
Then I create a patch for the code I want to work on:

  
  
hg qnew issue-123 -m "Fix issue 123."

  
  
We work away on our changes to the code, adding our tests and so on, and then
when we want to save those changes to our patch, we simply use the `qrefresh`
command:

  
  
hg qrefresh

  
  
Then we spot some whitespace issues, and perhaps a few unused variables.
Rather than making the changes there and then, we 'unapply' our current patch
-- the one that contains all of the changes to fix issue 123 -- by using the
`qpop` command:

  
  
hg qpop

  
  
Our entire working directory is now back to where it was when we created the
'housekeeping' patch, but all of our changes for 'issue-123' are safely stored
in a patch, waiting to be reapplied. And any changes that we make now, by
using the `qrefresh` command, will automatically be placed into the
'housekeeping' patch, rather than the 'issue-123' patch. As you can see
Mercurial is helping us manage our patches, as we try to get them ready for
code review.

  
Once we've made our housekeeping changes, we can reapply our 'issue-123'
patch, by using the `qpush` command:

  
  
hg qpush

  
  
The working directory now reflects all of the changes that we had been working
on before.

  
A slightly more complicated scenario arises when we realise that the work we
are doing should really be a separate piece of work. The easiest scenario is
when we've added a file or two, but realise it's not really about 'issue-123',
but

