---
layout: post
title: "Linking issues and revisions in Google Code"
date: 2009-08-23
comments: false
tags:
 - xforms
 - mark birbeck
---
  
We use [Google Code](http://code.google.com/) a lot in our projects, because
it provides a great range of tools, with a straightforward interface.

  
It's particularly useful to have version control and issue-tracking in the
same system, and it's very easy to cross-reference the two by referring to
revisions in issue comments, and to issues in the commit messages.

<!-- more -->

  
On top of that, Google Code provides some nice code review facilities, and
here too, it's possible to refer back to revisions and issues.

  
In this post I'll outline how we use these tools together in UXF, the
[Ubiquity XForms AJAX library](http://ubiquity-xforms.googlecode.com/).

  

## Everything should have an issue

  
  
The first thing that's needed is an issue. This might be an issue in the
traditional sense, of a bug. But it might also be a desirable feature, that
someone would like to add. Whatever it is, we shouldn't really be starting to
code until we've written down what we're going to do, or what problem we're
trying to solve.

  
Let's use [issue 515](http://code.google.com/p/ubiquity-
xforms/issues/detail?id=515) as an illustration. At the top we see it has the
following description:

  

    
      
      We need a UI control that can adhere to data binding restrictions for   
      xsd:boolean / xforms:boolean. The control may render a checkbox, for   
      example.  
      
      Sample code snippet:  
      
          <xf:input ref="check" datatype="xforms:boolean">  
            <xf:label>Check: </xf:label>  
          </xf:input>  
      
      Based on conversation with John in last week's call, needed in this   
      iteration (setting priority to critical).  
    

  
  

## Code reviews and issues

  
  
This description is plenty to get started with, so
[Rahul](http://code.google.com/u/rahul.akolkar/) gets on with his development
and testing, until he is ready for a code review. When he is ready, he commits
his code to the review area, with the following SVN message:

  

    
      
      [ issue 515 ] Proposed changes for adding a UI control that can bind to boolean  
      data (renders a checkbox).  
    

  
  
You can view this commit at [r2919](http://code.google.com/p/ubiquity-
xforms/source/detail?r=2919), and as you can see, Google Code provides a
convenient link to all of the files involved in the commit. GC also provides
tools for attaching comments to any line in those files.

  
This revision therefore becomes the location at which the review is conducted,
perhaps involving many comments from many reviewers.

  
But note also that Rahul's reference to "issue 515" has become a link to the
issue itself. This means that a reviewer has everything they need to start
reviewing -- links to all of the files that have changed, a reference to the
issue being addressed, and some tools that allow them to add comments to the
files.

  
However, anyone viewing the issue directly would not necessarily know that it
was being worked on, or what the progress was. So to keep things up-to-date,
Rahul added the following comment to issue 515:

  

    
      
      Code for such a UI control has been proposed for addition in r2919 and review   
      requested. Changing Status to InReview.  
    

  
  
The issue is now not only marked as being `InReview`, but we also have a
convenient link to the code review itself. If you were interested in this
issue, you would now be able to add your comments to the review, try out the
proposed code, and so on.

  

## Committing the code to trunk

  
  
At some point the review will finish, and the code will be ready to go into
the trunk. (There may be other reviews first, but at some point the cycle
should end.)

  
In our example, Rahul commits his reviewed code to trunk at
[r2920](http://code.google.com/p/ubiquity-xforms/source/detail?r=2920), with
the following message:

  

    
      
      [ issue 515 ] UI control that can bind to boolean data. Committed after favorable  
      review of r2919, with suggested filename change.  
    

  
  
Note that we still have a reference to the issue number, but most importantly,
we have a reference to the code review at r2919. This means that anyone
looking at the code that is committed, and who wanted to understand why a
particular change was made, could find the history of the discussion in the
code review.

  
But we're not finished yet.

  
If we go back to the code review at r2919, we'll find that the last comment
Rahul has added says:

  

    
      
      Committed to trunk in r2920.  
    

  
  
Now, anyone who stumbles across the code review directly knows that the code
they are looking at really did find it's way into the trunk, and wasn't left
hanging there uncommitted, or was abandoned in favour of some other solution.

  

## Closing the issue

  
  
The final step is of course to close off the issue, and here it's important to
refer to the revision at which the code was committed. In our example, the
last comment on issue 515 is this:

  

    
      
      Committed to trunk in r2920 after favorable review.  
      
      Resolving issue as Fixed.  
    

  
  
Now anyone interested specifically in the issue can see not only that it is
closed, but also where the code is that addressed the issue.

  

## Conclusion

  
  
This technique may seem a little laborious, but it actually takes longer to
describe than to do.

  
And by following these steps we ensure that we know how a sequence of comments
ends, whether in an issue or code review.

  
We need to know how an issue was addressed when it gets marked as fixed, we
need to know what happens to code after a positive code review, we need to
know where some code came from that is committed to trunk; in short, no matter
whether you are looking at one of committed code, an issue, or a code review,
these procedures ensure that you can easily find the other two.

