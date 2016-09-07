---
layout: entry
title: "SugarCRM, RightScale and EC2"
date: 2008-02-27
comments: true
tags:
 - mark birbeck
---
[![SugarCRM Logo](http://www.sugarcrm.com/crm/images/SugarCRM_logo.gif)](http:
//www.sugarcrm.com/)

I've had an interesting time lately, looking at
[SugarCRM](http://www.sugarcrm.com/) for a potential customer. The project
involves having an internal system for the sales team, and then a public
system that needs to cope with tens or even hundreds of thousands of members.
My initial thoughts were to make use of
[Salesforce.com](http://www.salesforce.com/) for the internal side, and then
wire that in to [Drupal](/technology/drupal) for the public-facing part. But
in researching how to hook Salesforce into Drupal I came across Sugar.

<!-- more -->

  
Nowadays when I'm experimenting with anything to do with our server-side work,
I simply fire up a new [EC2](/technology/ec2) instance using
[RightScale](/technology/ec2), and then get to work on a RightScript that will
eventually do all the installation and configuration for me. As I add things
to the server, or change settings, I also add corresponding entries to the
scripts to perform the same task when booting. Then, I periodically fire up a
new instance from these scripts to see how well it's all doing.

  
This is a great technique, for a number of reasons. One which is very useful
in a busy work environment is that if you have to leave your experiments for a
week in order to do something else, you can just shut down the EC2 instance
you're experimenting with, and then relaunch it again when you are ready.
Thanks to the scripts you'll be exactly where you were.

  
What's more, the 'knowledge' that you accumulated during the creation of those
scripts is captured in the scripts themselves, and to get back up to speed on
what you were doing you need only look at your RightScripts--and the same goes
for your colleagues.

  
For example, say you come across a blog post suggesting that better
performance can be had from Sugar if some PHP setting is set to 7 instead of
6; if you change that in your configuration file in a live server the whys and
wherefores of that decision will be hidden in some file, deep in some
directory, where you're unlikely to find it again. Commenting the change
doesn't help, since you need to know to look for it in the first place.

  
However, in RightScale, you make this change part of your boot script. And
what's more, instead of the boot script copying an entire initialisation file,
you just get the script to modify the one setting that needs changing. That
way, if you ever change the script so that it installs a newer version of
Sugar, MySQL, PHP or whatever, you can still target that one setting.

  

## Installing and configuring Sugar

  
The initial boot scripts worked fine, and in just a few hours I was able to
create Sugar instances with 'one click', that:

  

  * retrieve and unpack the latest version of SugarCRM;
  

  * install PHP, including various components like email support;
  

  * alter various PHP settings, such as the amount of script memory to use;
  

  * install and secure MySQL;
  

  * schedule MySQL backups to S3;
  

  * update the DNS at DNS Made Easy.
  
  
  
But although this made things a lot easier, after playing with the
functionality it quickly became obvious that the slow performance I was
experiencing wasn't going to go away.

  
And I nearly gave up on Sugar.

  

## Caching

  
Luckily I decided to give it a bit longer, and started to delve into various
options for PHP caching. I soon settled on
[eAccelerator](http://eaccelerator.net/), but I couldn't find any way to get
this onto the system, other than to actually build the modules (and I'm sure
I'm not alone in preferring to use Yum when I can). But in the end I bit the
bullet, since all I really had to do was to modify the RightScripts so that
after installing and configuring Sugar, they pulled down the source for
eAccelerator, compiled the code, and then hooked the module into Apache.

  
On launching a new Sugar server, things were much faster.

  

## RightScripts and reuse

  
The key point here is that the time taken to create a fully working
RightScript that can launch an instance of a Sugar server that is immediately
ready to use, was about the same as running through the process of 'build
only'. But although each technique would have taken about the same time,
creating scripts is by far the stronger option, since at the end of the
process we have a means of creating as many Sugar servers as we like, any time
we like.

  
We don't know in advance which technologies will be best for our customers'
applications, what combination of technologies might be needed, and even how
many servers will be required. But by taking the RightScript/EC2 approach to
all of our server-side technologies, it means we can be as flexible,
responsive, and experimental as we need to be.

