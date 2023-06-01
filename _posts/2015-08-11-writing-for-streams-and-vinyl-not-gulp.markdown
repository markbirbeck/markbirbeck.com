---
layout: post
title: "Writing for Streams and Vinyl...Not Gulp"
date: 2015-08-11
comments: true
tags: 
 - streams
 - gulp
 - development
---

When writing your shiny new module to use with Gulp it's tempting to call it something like `gulp-my-module`. A quick check on NPM for modules with 'gulp' in their name shows that [7239 modules have already taken that approach](https://www.npmjs.com/search?q=gulp-*).

But how many of these modules are actually Gulp-only plugins? By which I mean, how many of these modules will *only* work with Gulp, and how many are actually aimed at manipulating [Vinyl](https://www.npmjs.com/package/vinyl) file objects?

## Vinyl Files

Let's pick a plugin at random, for example the [gulp-stylus](https://www.npmjs.com/package/gulp-stylus) module.

If we take a look at the code we'll see that it follows a pretty standard pattern, present in a lot of Gulp plugins. It:

* takes the `contents` property of each Vinyl `file` object passed in, and;
* runs it through Stylus, before;
* placing the results of the transformation back in `file.contents`;
* modifying `file.path` to reflect the changes, and then;
* passing the Vinyl file on.

Interestingly there is nothing in here that is Gulp-specific. It's true that the library uses `gulp-util` to create a `PluginError` object, but that could easily be done at a level higher then the module, and just below Gulp.

So what we really have is not a `gulp-stylus` module but a `vinyl-stylus` module. Writing a module to support Vinyl rather than Gulp means that the plugin could be used in other tooling systems (not to mention that the Gulp ecosystem would feel a little less top heavy).

I've been using this approach for a while now, having created Vinyl-processing modules that deal with email ([vinyl-email](https://www.npmjs.com/package/vinyl-mail)), AMQP ([vinyl-amqp](https://www.npmjs.com/package/vinyl-amqp)), ElasticSearch ([vinyl-elasticsearch](https://www.npmjs.com/package/vinyl-elasticsearch)) and Evernote ([vinyl-evernote](https://www.npmjs.com/package/vinyl-evernote)), rather than their Gulp-specific equivalents.

## What about Streams?

But after a while the fog begins to lift and you start to realise that we can go further.

If we look at the steps outlined above we can see that the only part that is specific to this Stylus task is the second step of running the data in `file.contents` through the Stylus processor; all of the other steps are boilerplate.

The same goes for converting a Vinyl file to and from Evernote, saving or reading Vinyl files from ElasticSearch, sending and receiving Vinyl files as email, pushing Vinyl files into an AMQP queue, and so on.

It seems then that what we should really be writing are handlers that can manipulate streams which can then be used in Vinyl processors. And of course those processors can in turn be used as Gulp plugins.

That might all seem a little complicated when it's being described, but the reality is that once you actually start doing it this way it makes the code remarkably streamlined, and rather than getting too distracted by the boilerplate you can focus on what your module really needs to do.

(And of course you're future-proofing your module so that they will still work when the next great build tool comes along to replace Gulp.)

## Streams and Vinyl

What I've done for the most recent module that I wanted to use with Gulp is to meet somewhere in the middle. The core of the module converts Google Sheets to a stream of JSON objects, and is used by simply requiring the module:

```javascript
var sheetStream = require('stream-google-spreadsheet');

sheetStream(glob, opt)
  .pipe(process.stdout);
```

As you can see it's not called `gulp-google-spreadsheet` or `vinyl-google-spreadsheet` because it focuses on streams rather than Gulp or Vinyl.

However, after a lot of going backwards and forwards I decided that converting the objects in the stream to and from Vinyl file objects was such a common requirement that we should provide a thin wrapper that does just that. So there is also a `.src()` method available which will return each of the JSON objects 'wrapped' in a Vinyl object.

The module is not a Gulp plugin as such, but as discussed above, it doesn't need to be -- it just needs to support Vinyl to be usable with Gulp:

```javascript
var gulp = require('gulp');
var sheets = require('stream-google-spreadsheet');

gulp.task('download', function() {
  sheets.src(glob, opt)
    .pipe(gulp.dest());
});
```

## Conclusion

For now, concentrating on stream processing but with a `.src()` function to expose the stream as Vinyl, is how I'm going to approach all my modules. That way I can use them with Gulp as well as any of my other apps that use streams.

That is at least until I come across something better...
