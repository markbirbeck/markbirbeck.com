---
layout: entry
title: "Using a Gulpfile to Quickly Create an API"
date: 2015-08-04
comments: true
tags: 
 - gulp
 - api
 - development
---

Sometimes we want to pipe files from a web server into Gulp.

Sometimes we want to pipe files out of Gulp into a web server.

But sometimes we want Gulp to simply *be* a web server.

<!-- more -->

## Overview

Look at this:

```javascript
gulp.task('abc', function() {

});

gulp.task('xyz', function() {

});
```

Doesn't that look a little bit like an API?

Recently I spent a lot of time looking at Sails, Hapi, LoopBack and more, in search of a framework for creating APIs. There's no question that they're all  great libraries. But I also realised that since I was putting a lot of energy into turning my apps into streams -- static site generators, email pipelines with Handlebars templates, transformation pipelines extracting text from PDFs and finding dates, and so on -- then why not use streams in my APIs too?

Many of the pipelines that I was creating would work just as well in an API, so I decided to give it a go. If I could get it to work it would mean that I could have a single set of Gulp tasks for doing common work, that could be used in command-line tools or in an API. For example, I'm often running Markdwon through template transformations for static site generation, email, and so on. With this technique I can use the template tasks for both scenarios, without changing a thing.

The [gulp-endpoint](https://github.com/markbirbeck/gulp-endpoint) module is the first cut of this, but I know its's going to grow.

## Usage

To use `gulp-endpoint` simply install it in some existing project:

```shell
npm install --save markbirbeck/gulp-endpoint
```

This will make the `endpoint` command available, which can be run from the command-line:

```shell
endpoint
```

The command will load the local `gulpfile`, launch an Express server on the specified port (or the default of `3000`), and then set up a route for each task.

### Options

#### Port (default is 3000)

To select another port to listen on, either define the `PORT` environment variable:

```shell
PORT=3005 endpoint
```

or use the `--port` command line option:

```shell
endpoint --port 3005
```

## Behaviour

Any request that hits a URL with the same name as the Gulp task will cause the corresponding task to be run. The `gulp.src` and `gulp.dest` functions are monkey-patched so that data provided with a `POST` to the task will become a source file into the pipeline, and any data pushed to `dest()` at the end will be returned through Express to the API caller.

## Example

For example, say we have the following simple `gulpfile` which has three tasks -- `concat`, `compile-page` and the default task which invokes `compile-page`:

```javascript
var gulp = require('gulp');
var concat = require('gulp-concat');
var swig = require('gulp-swig');
var frontMatter = require('gulp-front-matter');

gulp.task('concat', function() {
  return gulp.src(['hello.txt', 'world.txt'])
    .pipe(concat('result.txt', {newLine: ' '}))
    .pipe(gulp.dest('output'));
});

gulp.task('compile-page', function() {
  return gulp.src('page.html')
    .pipe(frontMatter({property: 'data'}))
    .pipe(swig())
    .pipe(gulp.dest('build'));
});

gulp.task('default', ['compile-page']);
```

Running `endpoint` from the same directory as this file will result in a server with an endpoint created for each of these three tasks:

```
http://localhost:3000/
http://localhost:3000/compile-page
http://localhost:3000/concat
```

The first two endpoints both refer to the `compile-page` task, since the `default` task is wired to the root of the API.

Now, if a `GET` request is made to any of these three URLs the corresponding task is run. Since `GET` doesn't provide any input then `gulp.src` won't be monkey-patched and the task will be executed with whatever `gulp.src` parameters are in the `gulpfile`. In the example above this means that the files `hello.txt`, `world.txt` and `page.html` would be used in the pipeline.

The *results* of these tasks *do* go through Express though; whatever would ordinarily find its way to `gulp.dest` will be returned in the HTTP response.

It's also possible to override the values in `gulp.src` by passing data in to the API with a `POST` request. In this case the data in the `POST` will be converted to a Vinyl file and then fed through the pipeline, and any files specified as input in the `gulpfile` will be ignored.

## Conclusion

`gulp-endpoint` wraps Gulpfiles to make it easy to create an endpoint from an existing pipeline. In fact, to quickly make tasks from a Gulp-based command-line tool available as an API on a hosting service like Heroku, we need only do the following:

* install `gulp-endpoint` into the command-line tool's project;
* add `endpoint` as the `start` command in `package.json`;
* push the project to the hosting service.

