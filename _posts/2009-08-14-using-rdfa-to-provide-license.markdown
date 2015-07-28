---
layout: post
title: "Using RDFa to Provide License Information to Google Image Search"
date: 2009-08-14
comments: false
categories:
 - mark birbeck
 - rdfa
---
About a month ago, Google made it possible for anyone searching for images to
filter the results based on the license type (see [Find Creative Commons images with Image Search](http://googleblog.blogspot.com/2009/07/find-creative-commons-images-with-image.html)). But it was only in the last couple
of days that they spelt out exactly how you can let the Google crawlers know
what licenses you are making your own images available under, in [a video by Peter Linsley, from Google Image Search](http://www.youtube.com/watch?v=quyhasVn2jw) -- which is to use RDFa.
Peter's created a great tutorial, but it's actually possible to mark-up
license information even more simply than he shows in his video.

<!-- more -->

## Introduction

The tutorial begins with the code for an image tag:

```html
<img src="image.jpg" />
```

Peter then
suggests adding a link to the relevant license, as follows:

```html
<a rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/"
 >Creative Commons Attribution-Share Alike 3.0</a>
```

Peter rightly points out that with this mark-up, Google has no way to tell
whether the license applies to the page or the image -- a problem that would
be compounded if there were a number of images on the same page. However, he
shows how this can be resolved by adding a container for the mark-up, and
placing an `@about` value onto that container:

```html
<div about="image.jpg">
  <img src="image.jpg">
  <a rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/"
   >Creative Commons Attribution-Share Alike 3.0</a>
</div>
```

There's nothing wrong with Peter's approach and it will work in all
situations. But there will be many occasions where a slightly easier solution
is possible.

## Placing @about on the anchor

This first scenario assumes that we still want the clickable link, but not the
containing `div`. In that case we need only place the `@about` value onto the
link itself, to achieve the same effect as having the container:

``` html
<img src="image.jpg">
<a about="image.jpg" rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/"
 >Creative Commons Attribution-Share Alike 3.0</a>
```

The nice thing about this
technique is that the clickable link that refers to the license doesn't need
to be directly next to the image, or even close on the page (which it does
with the container approach), which gives publishers a little more flexibility
when it comes to the document's layout.

## Images and @rel

If we don't want the clickable link that points to the license, then we can
place the `@rel="license"` directly onto the image:

``` html
<img src="image.jpg" rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/">
```

This is a
great solution for software tools such as editors or content management
systems, because it means that licensing information can be added to the
image, right at the point where the image is embedded into the mark-up. Not
only that, the image reference and the license are combined in a self-
contained unit that can move around the document with no side-effects.

## Using the same license for many images

Note that if the same license is to be applied to many images, then the
containing element technique becomes useful again; we can specify a reference
to the license on the container, and then use `@rev` to make the connection
with the images:

``` html
<div about="http://creativecommons.org/licenses/by-sa/3.0/" rev="license">
  <img src="image1.jpg">
  <img src="image2.jpg">
  <img src="image3.jpg">
  <img src="image4.jpg">
</div>
```

This gives Google exactly the same information as this:

``` html
<img src="image1.jpg" rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/">
<img src="image2.jpg" rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/">
<img src="image3.jpg" rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/">
<img src="image4.jpg" rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/">
```

## License information in the head

One final approach worth mentioning for the sake of completeness, is to place
all of the license information into the head of the document:

``` html
<head>
  <link about="image1.jpg" rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/">
  <link about="image2.jpg" rel="license" href="http://creativecommons.org/licenses/by/3.0/">
  ...
</head>
```

This may be
useful in some CMS systems, where all of the licensing information for any
element that appears on a particular page can simply be 'dumped' into the page
in one go.

## Conclusion

It's great to see Google improving the user's search experience by surfacing
license information in this way, and it's great to see that RDFa is helping
publishers to provide this information to Google's crawlers, however they
choose to mark it up.
