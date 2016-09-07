---
layout: entry
title: "The 10 minute guide to Sidewinder (or 'How to turn a web app into a desktop app without programming')"
date: 2008-01-14
comments: true
---
Welcome to the quickest way to get up and running with Sidewinder, the open
source web applications framework. This post is intended as a quick step-by-
step guide to creating desktop applications with web languages, but it's full
of screenshots, so it should also be useful to anyone who wants an overview of
what Sidewinder can do.

<!-- more -->

  

## Install Sidewinder

  
Yes, it's obvious, but until we've installed Sidewinder there isn't a lot we
can do, so [go grab Sidewinder from Sourceforge](http://sourceforge.net/projec
t/platformdownload.php?group_id=204948)--it's only a couple of meg.

  
If you're a Linux or Mac user there is a piece of bad news though--currently
Sidewinder only runs in Windows. But the good news is that we put a lot of
work into porting Sidewinder so that it uses
[wxWidgets](http://www.wxwidgets.org/), which means that we now have the
architecture in place to run Sidewinder on many different platforms.

  
If you've now got Sidewinder installed, let's move on.

  

## Open a web application from the command-line

  
Sidewinder runs in a number of different modes, but so that we can take it for
a quick spin, we'll concentrate on the command-line mode. Open a command box,
and type:

    
      
    swviewer2 http://www.google.com/reader#meta(usesxhr)  
    

  
If you don't have an account with Google Reader I'd recommend getting one, but
if you're not convinced or that sounds like too much hassle, just pick some
other web application that you use regularly, such as a web-based email client
or calendar, your Basecamp tasks, or whatever. Assuming that you do choose
Google Reader, you will now have something like this:

  
![Screenshot of Sidewinder showing the Google Reader login page](/files
/google-reader-login.png)

  
Congratulations! You have just turned a web application into a desktop
application! In a moment we'll start to tune this application to meet our
needs, but first let's just see it working; login to Google Reader (apologies
that for some of you the `TAB` key won't work), and you should get the Reader
home page--something like this:

  
![Screenshot of Sidewinder showing a basic view of Google Reader after logging
in](/files/google-reader-basic.png)

  

## Setting the application size

  
As you can see, the reader is squeezed into our desktop application like a
heavyweight boxer in shirt and bow-tie, so let's make the Viewer a little
larger. Close Sidewinder, return to your command prompt, and this time type:

    
      
    swviewer2 http://www.google.com/reader#meta(usesxhr,width=900,height=500)

  
The window will now be larger, and Google Reader will have a little bit more
room to breathe:

  
![Screenshot of Sidewinder showing a larger view for Google Reader](/files
/google-reader-larger.png)

  

## Auto-hiding the application

  
Of course, this larger application is taking up a lot more of our screen real
estate. That's fine when we're using it, but when we're not we'd like it to
slide off the desktop. This is easy to achieve; just tell the application to
'autohide', and at the same time give it a screen edge to dock to (we'll use
the top of the screen):

    
      
    swviewer2 http://www.google.com/reader#meta(usesxhr,width=900,height=500,autohide,position=top)  
    

  
Now, when you move your mouse away from the Google Reader desktop application
it will slide off the screen to the top, and when you move your mouse back to
the top edge, it will slide back in again.

  
  
  

## Creating desktop shortcuts and menu items

  
Now that we've set all of our parameters, we can turn this command into a
desktop shortcut that will open Google Reader whenever we need it. One way to
do this is to click on the right-hand mouse button on your desktop and select
'New' followed by 'Shortcut'. In the wizard that comes up paste the full
command that we've just been using into the box labelled 'Type the location of
the item', and then click 'Next'. In the next panel choose a name for your
shortcut, such as 'Google Reader', and then click 'Finish'.

  
If you double-click on your new icon you will go straight to Google Reader,
with the viewer sized and positioned how you want.

  
There is a slightly easier way, though, and that is to simply open Sidewinder
with no parameters. You'll see a dialog-box that looks like this:

  
![Screenshot of Sidewinder in 'Create Application' mode](/files/swwav-create-
shortcut.jpg)

  
Now fill in the settings for how you would like your application to run:

  

  * the `title` is used to name the shortcut, as well as for the title bar of the application when it is running;
  

  * the `URL` value is the location of Google Reader itself, which is `http://www.google.com/reader`;
  

  * the `width` and `height` should be changed to 900 and 500;
  

  * the `position` should be set to "Top, middle";
  

  * turn `autohide` on;
  

  * decide whether you want desktop shortcut, an entry on your Start menu, or both.
  
  
  
You should now have something like the following, and all you need to do is
click 'Create' and the entry or entries will be created for you:

  
![Screenshot of Sidewinder creating a desktop application for Google
Reader](/files/swwav-create-shortcut-google-reader.jpg)

  

## iPhone apps become gadgets

  
We've looked at how applications designed to run in the browser can easily be
turned into desktop applications with Sidewinder. But there is a further class
of web application that is emerging, which is ideal for Sidewinder--apps that
have been designed to run on the iPhone. A nice looking example is the iPhone
weather viewer from Albino Blacksheep; try this:

    
      
    swviewer2 http://www.albinoblacksheep.com/iphone/weather/?zip=UKXX0085#meta(width=352,height=480,position=right-top,autohide,chrome=false)  
    

  
If you have IE configured for development you'll have to acknowledge a few
script errors, but once you have, you should see the weather for London:

  
![Screenshot of Sidewinder showing the Albino Blacksheep iPhone weather
application](/files/iphone-weather-london.png)

  
Now we have a desktop gadget that shows us the weather, and that will autohide
when we move our mouse away.

  

## Setting opacity

  
We can also set opacity on web applications and gadgets, so that the
background 'shows through'. Just add `opacity` with a percentage, like this:

    
      
    swviewer2 http://www.albinoblacksheep.com/iphone/weather/?zip=UKXX0085#meta(width=352,height=480,position=right-top,autohide,chrome=false,opacity=90)  
    

  
  
![Screenshot of Sidewinder showing the Albino Blacksheep iPhone weather
application with opacity set to 90%](/files/iphone-weather-london-
opacity-90.png)

  

## Using a Firefox renderer for the Facebook iPhone application

  
The final thing we'll show is how Sidewinder allows different rendering
engines to be 'plugged in'. If you look at the Facebook iPhone app in
Sidewinder:

    
      
    swviewer2 http://iphone.facebook.com#meta(usesxhr,width=320,height=480,chrome=false,autohide,position=right-top)  
    

  
you'll see that the styling is terrible. This is because the default rendering
engine in Sidewinder is Internet Explorer. However, if other renderers are
available Sidewinder can easily make use of them. If you want to try this
[install a Firefox
renderer](http://www.iol.ie/~locka/mozilla/control.htm#download), and then go
to Facebook again. You'll need an additional parameter, which tells Sidewinder
which rendering engine to use:

    
      
    swviewer2 http://iphone.facebook.com#meta(usesxhr,width=320,height=480,chrome=false,autohide,position=right-top,renengine=2)  
    

  
Now the Facebook page looks much more presentable:

  
![Screenshot of Sidewinder showing the login page for Facebook's iPhone
version](/files/iphone-facebook-login.png)

  
If you have an account, you can log in, and you should see something like
this:

  
![Screenshot of Sidewinder showing the logged-in user's news page from
Facebook's iPhone version](/files/iphone-facebook-news.png)

  
Select one of the items and note the iPhone-like animations.

  
(When you close the viewer after using the Firefox renderer, there is a crash;
[we're working on it](http://sw.swcube.com/ticket/17).)

  

## Conclusion

  
There is a lot more that Sidewinder can do, including:

  

  * running as a standalone browser;
  

  * running embedded in other browsers;
  

  * opening many windows at the same time and allowing communication between them;
  

  * using JavaScript files to write desktop applications;
  

  * and more!
  
  
But in this post we wanted to focus on the ease with which you can turn your
favourite web applications into desktop applications and gadgets.

