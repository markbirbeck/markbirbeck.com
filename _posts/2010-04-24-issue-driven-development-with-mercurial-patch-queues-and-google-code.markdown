---
layout: post
title: "Issue-driven development with Mercurial patch queues and Google Code"
date: 2010-04-24
comments: true
tags: development
---

_Note that since this post was written I've moved to Git and GitHub. However,
I still use the same workflow._

One of the things I like best about using Mercurial patch queues is the way
that you can continually refine the commit message before you send anything
back to your repository. Since the message won't actually appear in your
revision history until you convert the patch to a proper commit, you can hone
the message as you work. This becomes particularly powerful when combined with
Google Code's ability to update issues based on your commit messages, and
greatly assists with *issue-driven development*.

<!-- more -->

Let's use a real scenario to illustrate.

I wanted to improve the performance of toggling the XForms `case` element, in
the XForms part of [backplanejs][1]. Given that I generally use an IDD approach,
the first thing I did was to create an issue ([issue 45: "Improve performance of case toggling"][2]).

Over in Mercurial I then created a new patch. I gave it the name 'issue-45' and
asked Mercurial to let me edit the commit message:

    hg qnew issue-45 -e

My assigned editor opened and I entered the following:

    Improve performance of case toggling.
    
    Fixes issue 45.

I tend to keep the first line of these commit messages fairly short, because it
is used by Mercurial in various places, such as when you list your current
patches with `hg qseries`. But after that you can put whatever you like.

The "Fixes issue 45." part is the interesting bit. When Google Code sees this
message it will close issue 45 and automatically provide a link to the changeset
in the issue's comments. This message will then be added to the changeset, and
the reference to 'issue 45' will become a link to the issue. (You can also add
comments without closing the issue, generate a new issue, add labels, and so on.
See [issue-tracker help][3] for more information.)

None of these changes happen until we commit our code to the Google Code
repository, which means we can work on our changes and the associated
commit message until we're happy.

As I worked on improving performance, it became clear that when we toggle the
state of a form control from enabled to disabled, we should be a bit more
efficient about how we manage changes to the CSS classes. The normal operation
is to set the CSS to 'enabled' or 'disabled', but the code was first calling
code to remove *both* values, before then writing back whichever value
represented the current state of the control.

Since there were actually two improvements I could make I raised issues 46 and
47, with appropriate explanations. When I'd finished updating the code I did
the usual 'refresh' to put the code in the patch, but I also told Mercurial
that I wanted to update the commit message:

    hg qrefresh -e

My editor opened and I was able to change the message to this:

    Improve performance of case toggling.
    
    Fixes issue 45.
    Fixes issue 46.
    Fixes issue 47.

After making the required changes, all that remained was to convert my patch
into a proper commit in my repository:

    hg qfinish -a

Of course Google Code still hasn't seen these messages, and that requires a
'push':

    hg push

This final action automatically closes all three issues for me, and adds
cross-referencing messages to both the changeset and the issues.

You can see the result by looking at [issue 45][2] or [revision d6ea4e794d][4].
When used with the diff feature when looking at a changeset, this gives a
detailed record of the changes that were made, and why -- ideal for IDD.

 [1]: http://backplanejs.googlecode.com/
 [2]: http://code.google.com/p/backplanejs/issues/detail?id=45
 [3]: http://code.google.com/p/support/wiki/IssueTracker#Integration_with_version_control
 [4]: http://code.google.com/p/backplanejs/source/detail?r=d6ea4e794d