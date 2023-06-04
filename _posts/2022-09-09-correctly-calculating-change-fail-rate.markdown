---
layout: post
title:  "Correctly Calculating Change Fail Rate"
date:   2022-09-09 14:08:00 +0000
comments: true
tags: 
 - dora
---

CFR is a valuable metric, but if we forget its purpose we can calculate it incorrectly.

<!-- snip -->

When looking at how to improve the performance of application delivery it can be tempting to take a big-bang approach. Perhaps there should be a complete reorganisation, with new roles, and new teams with clever names based on Marvel characters or mountain ranges?

Or maybe the code should be re-written from scratch, using some new library or framework? Or even re-written using some _old_ library or framework, so that we can return to those golden times when things seemed simpler and we cursed a lot less.

And whether it's teams or code, there will probably be a suggestion in there somewhere for a big Agile-shakeup, involving more retrospectives and better ticket estimation Poker T-shirt wearing.

But if we know one thing for certain, it's that when it comes to secret sauce, there is no secret sauce. Often, to get started we simply need a metric that is easy to capture, and that is a good indicator of what we do.

## Accelerate, The Science of Lean Software

A fantastic source of such metrics is the book _Accelerate_, by Nicole Forsgren, Jez Humble and Gene Kim. If you don't have a copy, go get one. I'd recommend getting an actual printed copy (don't share), because you will be underlining, and writing all over it for quite a long time.

The metric I'm particularly interested in for this post is what they call in the book _Change Fail Rate_. And what I mainly want to look at is the confusion around how it is calculated.

## What Is Change Fail Rate?

Change Fail Rate (CFR) is a measure of how successful your team is when it makes changes to production code.

If you're following along with DevOps thinking, or more specifically the SRE approach, you'll know that the major tension we face is between the conflicting desires of application stability and adding new features.

At one extreme, if we add no new features to a stable application, then we will usually continue to have a stable application. And at the other extreme, if we rapidly add new features without paying attention to how we go about it, then we'll keep undermining our application.

Somewhere in the middle will be the sweet spot for your particular application, based on your organisation's appetite for risk; stability for an online streaming service means something different to stability for an ecommerce site, which is different again to what stability means for an online trading platform.

## Preventing Problems Reaching Production

So CFR is used to reflect what happens when you go to make a change.

But we're not interested (for this metric) in splitting up the whole history of a change, from code review to merge to test to deployment. We're concerned here with the final step; when we come to deploy new features, how often do we disrupt the current application?

To put it in terms of the tension between stability and features, how often does our process of adding enhancements actually result in instability in our service?

According to _Accelerate_ this metric correlates strongly with the general health and maturity of your software delivery process (p. 38), which of course makes sense; if you have high quality, well-tested code, combined with careful coordination between teams and APIs, then you should be in a good position to avoid releasing problems into production. Teams that are high and medium performing across their delivery process will have a CFR of between 0-15%, whilst low performing teams will have a CFR of 31-45% (p. 19).

(It's worth saying that a CFR of 0% is not necessarily a sign of perfection. If we _never_ have to rollback or hotfix, then we might be going too slow with our new features! As the book shows, even high performing teams might have a CFR of up to 15%.)

## Incorrectly Calculating CFR

The first thing we will no doubt think of when making this calculation is that we should tot up our deployments, releases or launches. Whatever terminology you use, it seems obvious to begin with the number of deployments we've done, and work out a ratio against those deployments that were unsuccessful.

Let's say we plan to deploy once a month. Let's also say that we have carried out 10 deployments this year, comprising the one a month that we expected (say for January to August), plus two additional deployments to resolve problems (say in April and June).

The expectation then is that the CFR is 20%, since there were 2 failures out of our total of 10 deployments.

But this is wrong, and can seriously flatter the situation.

## Better and Worse

To understand why this calculation is wrong, let's go back to the goal; we want to understand how many of our attempts to change our application resulted in instability. Or in terms of the tension...when we were trying to make things better with new features, how often did we actually make things worse?

To find out how often we tried to make things better, we simply tot up the number of planned deployments. In our example we tried 8 times to make things better, with our monthly releases from January to August.

The next question is how many of those attempts to release new features resulted in a problem, such as needing to apply a hotfix, or roll back our release?

We know that's 2--April and June.

So our CFR--the number of attempted changes that caused a problem--is not 20%, but 25%, i.e., 2 failures divided by 8 successes.

That difference of 5% probably doesn't seem like a lot, and you might be thinking that as long as you get this figure roughly right, it's still a useful metric. But to see the problem, let's try some other scenarios.

Let's imagine that in each of our monthly deployments, we had to carry out a rollback or hotfix. Using our erroneous calculation we might imagine that we have a CFR of 50%, since we have 8 failures divided by 16 deployments.

It's a lot, but maybe it's not terrible. After all, half of our deployments have been successful.

But the reality is that we have a CFR of 100%.

It's easy to see why; we made 8 attempts to deploy new features, and each of those attempts resulted in a rollback or hotfix. In other words, every single one of our deployments caused a ‘change fail'. And that's 100% in anyone's book.

If it's still not quite gelling, then think of it the other way around; if we didn't have to apply any hotfixes or rollbacks, what would be the number of deployments? That's obvious, you say...it's 8. We planned to do one per month, and we did.

Now, what if in addition to those 8 deployments we had to apply 8 hotfixes or rollbacks? How many deployments are there now?

We can't say 16. We can't say that by failing to deploy smoothly 8 times, we've effectively doubled our number of deployments!

To put it yet another way, if we had a target to deploy twice a month, have we now met that target by failing to deploy correctly every month?

## Conclusion

CFR is a powerful metric to track. Not only will it help to improve your release process, but since there is a strong correlation with other software delivery processes, it's a good first metric to look at if you went to start improving across the board. But for the metric to be useful it must be calculated correctly, and to do that we need to remember what exactly we are measuring; we are quantifying how often the changes we make to our application actually introduce instability.