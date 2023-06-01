---
layout: post
title: "SSG-independent Themes"
date: 2015-08-06
comments: true
tags: 
 - ssg
 - themes
 - development
---

There are [lots of static site generators (SSG)](https://www.staticgen.com/), written in a variety of languages and many of them are incredibly powerful. But when it comes to theming they all seem to suffer from the same problems.

## Installation

The first problem is that installing a theme generally involves cloning a repo from Git and then copying the theme into a subdirectory within your blog. The result is that the theme is now a 'part' of you blog and stored in your repo. If the theme author makes some improvements that you want to incorporate then you'll need to repeat the same process (clone, followed by copying to a subdirectory) and then make another commit.

But wouldn't it be nicer if a blog consisted simply of your Markdown files? That's all good you might say, but how do we indicate what theme to use? How do we indicate which plugins to install to alter the blog generation process or as helpers available to templates?

A powerful way that we can provide extra information and control any extra modules would be to use `package.json` and NPM. Relevant details about how to generate the blog, what sort of theme should be used and so on could all be in there.

And by entering the world of Node packages we not only get the benefit of being able to install themes easily, but we can also do package-related things such as indicate that our blog requires a specific version of a theme.

## Language Dependent

The second problem with themes as implemented in the current wave of SSGs is that even though some parts of the theme are SSG-independent, they can't actually be used in different SSGs.

For example, Jekyll themes use the Liquid templating language which means that in principle they could be used by an SSG written in Node, provided that SSG were to use a library that was capable of processing Liquid.

But as things stand most themes are tightly bound up with helper functions that are written in the language of the SSG that they should work with, and often the data that drives the theme has an SSG-specific layout (and often makes use of 'global' variables).

## Divergent Focus

Another problem is that different themes each tend to have a different focus. Depending on their creators one theme might have lots of features such as partials for Disqus and Google Analytics, whilst another theme might be less technical, but have a great design. Yet ideally the 'technical' features such as a partial for comments should be shareable amongst many themes.

## So What's the Solution?

### Factoring

The first step to trying to improve on this situation is to split layouts, partials, helpers and styling into separate modules, and then to construct theme modules as combinations of these supporting modules.

For example, a Liquid partial that adds Disqus comments would be the same across any Liquid theme since it's just a bunch of Javascript that is provided by Disqus. So why not factor it out to its own repo with its own discussion forum, issue-tracker, and so on, and then use NPM to bring it back in to any theme or blog that wants to make use of it.

### Standard Injection Points

One of the problems with partials though is that every time you want to add one you need to modify the template that makes use of it. For example, to add Disqus comments to a Jekyll theme you'd normally add the partial to the `footer` partial. Similarly, to add Google Tag Manager you must place the partial just after the opening of the `<body>` tag.

This could be solved though by placing a few `include` statements at key points in the layout templates, and then to dynamically provide any matching partials to the template engine to insert at the correct point. This way layout templates would never need to change, even as new partials are created.

### Standard Data Layer

A further improvement would be to provide a normalised data layer that sits between the preprocessing of the blog posts and the step where the posts are passed to the templates. This would ideally be based on some kind of 'standard', such as [BlogPosting at Schema.org](http://www.schema.org/BlogPosting).

All of these improvements will come, bundled in a new SSG that binds everything together.

## layout-liquid-jekyll-base

The [layout-liquid-jekyll-base](https://www.npmjs.com/package/layout-liquid-jekyll-basic) module is the first step in this process. It only includes layout files that come with a standard Jekyll site which means there's no CSS or SASS, no helper functions, and no partials to support features like Disqus and Google Analytics.

The new site generator and this layout are already being used to drive this blog, so even though the post you are reading looks and smells like a basic Jekyll blog, it's 100% nodejs. All of this means that as the various pieces improve, so will this blog.

## Terminology

To make it easy to talk about all of this, here's how I'm using the various terms:

### Theme

A theme is a combination of layouts, partials, helpers and styling.

### Layout

A layout comprises the files that determine the general layout of a page. For example, Jekyll takes the approach of having two layouts -- one for static pages and one for blog posts.

### Partial

A partial is a file that could be included in more than one layout or even in a different theme. For example, the partial to add Google Analytics to a Liquid theme could be the same across all Liquid themes.

### Helper

A helper is a function that can be called from within a template. Although helpers need to be written in specific language -- Ruby, JavaScript, etc. -- they are often simple enough that they could be used in any templating language.

### Styling

The styling comprises SASS and CSS files.
