---
layout: entry
title: "Setting Up a Default App Template With Git"
date: 2016-12-07
comments: true
tags:
 - git
 - javascript
 - app templates
 - node
 - polymer
---
Whenever I start a new project I invariably go through the same steps. I don't just mean steps like 'initialise a Git repo...create an NPM project', but I mean the same steps in the sense of double-checking the Gulp project site to see if there have been any updates, or looking to see whether the Polymer team have released (yet another) starter kit, or command-line tool, or some such.

At various times I've tried to capture the latest state of my knowledge in Gists on GitHub, so that I can go back and update them as things change. But what I really want is to be able to grab a repo from GitHub that gets me started. And then it occurred to me...why not have a repo with a set of branches for the different types of app that I might want to create -- one branch for a Polymer app, another for a Nodejs app, etc. -- and then just continually rebase as I add new 'best practice', switch tools...or the Polymer team make another change to 'how you should get started'.

This blog post describes how I went about it, and -- if the idea appeals to you -- how you could do the same.

<!--more-->

## Some Principles Guiding the Approach

### Commit History as Documentation

The first 'principle' I'm going to adopt is that I want my commit history to clearly describe the steps to get a particular app up and running, and I want each step to be distinct so that it can be switched out later, if necessary. For example, rather than having a commit that says 'install test tools', I'll instead have a couple of steps, such as 'install mocha' and 'install chai'. That way if I ever switch to a different testing framework I can just 'rebase out' the step that installs the old framework, and switch in a step to install the new one.

And if a step requires a bit of fiddling around then I'll document that step in the body of the commit message, so that it's clear what was done. It also means that if I want to make the same change to a project that's already up and running I can easily find the command I need to replicate the approach.

### All Tools Reside Within the Project Itself

The next principle is that all the tools necessary to build and run a project should be within the project itself, with the exception of Node, NPM and Docker. This means that I won't be installing Gulp, Bower, or anything else like that, globally. This is the subject of another blog post, but briefly, the advantage of doing this are firstly that different projects can coexist with different versions of tools -- crucial if you are working for different customers -- and secondly it means that it's easy to test your app in Docker with a [vanilla Node image](https://hub.docker.com/_/node/) like `node:onbuild`.

Let's Get Started.

## Steps to Create a Template Repo

### Create a Repo

First, we need to create a directory and change into it:

```shell
mkdir js-app-template && cd js-app-template
```

I've called my project _js-app-template_ because it's going to be for all of my JavaScript apps, regardless of whether they are Node, Angular, or Polymer. But after a bit of thought I decided it's not worth putting other language templates in the same repo since there will be so little in common between them. I'd probably create another repo for my Python templates, and then in a similar manner to this JavaScript repo, have branches for Sublime Text plugins, Hadoop tasks, Django sites, NLTK apps, and so on.

Anyway, call yours what you like, and then turn the directory into a Git repo:

```shell
git init
```

Next, we create an empty 'read me' file, and commit it:

```shell
touch README.md
git add . && git commit -m 'init repo'
```

### Create a Node Project

Next we're going to create a node project. Just run the following command, and answer the questions:

```shell
npm init
```

You can just press `[ENTER]` to answer most questions if you like, or you can choose your preferred options. If you plan to accept _all_ of the defaults then you might as well use:

```shell
npm init --yes
```

If you've set up NPM with your email address and preferred license type then these will be used. I just filled in an answer to the description question and let everything else use the defaults. My resulting `package.json` looks like this:

```json
{
  "name": "js-app-template",
  "version": "0.1.0",
  "description": "A template for my JavaScript apps.",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Mark Birbeck <mark.birbeck@gmail.com> (http://markbirbeck.com/)",
  "license": "MIT"
}
```

Now we can commit it:

```shell
git add . && git commit -m 'init node project'
```

We'll insist that the latest version of node is our default, by adding the `engine` property to our `package.json`:

```shell
git apply - <<EOF
--- a/package.json
+++ b/package.json
@@ -6,6 +6,9 @@
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1"
   },
+  "engines": {
+    "node": "6.x"
+  },
   "author": "Mark Birbeck <mark.birbeck@gmail.com> (http://markbirbeck.com/)",
   "license": "MIT"
 }
EOF
git commit -a -m 'insist on node version 6.x'
```

We'll also specify the contributors by using a separate file rather than the entry in `package.json` since it's a little easier to maintain in open source projects:

```shell
echo "Mark Birbeck <mark.birbeck@gmail.com> (http://markbirbeck.com)" > AUTHORS
git add . && git commit -m 'add AUTHORS file'
```

Finally, we don't want any installed node modules to be committed to source control, so let's add the module directory to our `.gitignore` file:

```shell
echo node_modules > .gitignore
git add . && git commit -m 'ignore node modules'
```

We're now nodified!

### Controlling our Code Format

Next we want to add tools that check our code at both the formatting level and syntactically. We'll use [Editor Config](http://editorconfig.org/) for file formatting and [ESLint](http://eslint.org/) for the syntax checking.

#### Editor Config

Editor Config uses a simple file to guide our editor as to whether we're using spaces or tabs, CR/LFs on the end of our files, and so on. If you haven't got your editor set up for this then [go and get the plugin for whichever editor you are using](http://editorconfig.org/#download). The key thing is that not only does it keep us consistent, but if we decide to open source our project then we want contributors to be consistent, too (including those who use Windows).

A basic `.editorconfig` can be created like this:

```shell
cat <<EOF > .editorconfig
# EditorConfig is awesome: http://EditorConfig.org

# top-most EditorConfig file
root = true

# Unix-style newlines with a newline ending every file
[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
EOF
git add . && git commit -m 'add basic Editor Config config'
```

We'll add one more commit to set the indentation style to use spaces instead of tabs, and to set the indentation level to two:

```shell
cat <<EOF >> .editorconfig

# Preferences:
[*]
indent_style = space
indent_size = 2
EOF
git commit -a -m 'use indentation of 2 spaces in Editor Config'
```

#### Linting

ESLint will be used in our build tools to check the _syntax_ of our JavaScript before deploying, bumping version numbers, etc. To install ESLint do the following (remember that we're not using global apps, so resist the temptation to use '-g'):

```shell
npm install --save-dev eslint
git commit -a -m 'add eslint to dev tools'
```

ESLint needs a config file in which we can specify the rules we want it to enforce. It also has the ability to pick up a set of default rules, which we'll use:

```shell
cat <<EOF > .eslintrc.yml
extends:
  - eslint:recommended
EOF
git add .
git commit -m 'use the recommended eslint rules'
```

ESLint also has the ability to use a collection of rules from an NPM module, so we'll install the rules created by Google to enforce their coding style:

```shell
npm install --save-dev eslint-config-google
cat <<EOF >> .eslintrc.yml
  - google
EOF
google commit -a -m "use google's eslint rules"
```

Finally, let's say that we can use ES6 features like `let` and arrow functions:

```shell
cat <<EOF >> .eslintrc.yml

env:
  es6: true
EOF
git commit -a -m 'allow ES6 constructs when linting'
```

Now that we're linting, we can add a lint step to our `package.json`, so that we can lint our files from the command-line by typing:

```shell
npm run lint
```

This is achieved like this:

```shell
git apply - <<EOF
--- a/package.json
+++ b/package.json
@@ -4,6 +4,7 @@
   "description": "A template for my JavaScript apps.",
   "main": "index.js",
   "scripts": {
+    "lint": "eslint .",
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "engines": {
EOF
git commit -a -m 'add npm script entry for linting'
```

We can also insist that our code is linted when we bump the version number of our application (using `npm version`). Both of these are achieved with the following modifications:

```shell
git apply - <<EOF
--- a/package.json
+++ b/package.json
@@ -5,6 +5,7 @@
   "main": "index.js",
   "scripts": {
     "lint": "eslint .",
+    "preversion": "npm run lint",
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "engines": {
EOF
git commit -a -m 'enforce linting when bumping the version'
```

Finally, ESLint can also be configured to check syntax whilst we're coding, from within our editor. As with Editor Config, how exactly you do this will be [different for different editors](http://eslint.org/docs/user-guide/integrations).

### Testing Our Code

First we need to install a testing framework. We'll use [Mocha](https://mochajs.org/):

```shell
npm install --save-dev mocha
git commit -a -m 'add mocha as the testing framework'
```

Mocha tests usually go in a subdirectory called `test`, so let's create that, and also place into it an ESLint configuration file that allows us to use Mocha's global variables without getting errors:

```shell
mkdir test
echo <<EOF > test/.eslintrc.yml
env:
  mocha: true
EOF
git add . && git commit -m 'add test directory and eslint config file for tests'
```

As we did with the linting we can add a 'test' script entry to the `package.json` that runs `mocha`:

```shell
git apply - <<EOF
--- a/package.json
+++ b/package.json
@@ -6,7 +6,7 @@
   "scripts": {
     "lint": "eslint .",
     "preversion": "npm run lint",
-    "test": "echo \"Error: no test specified\" && exit 1"
+    "test": "mocha"
   },
   "engines": {
     "node": "6.x"
EOF
git commit -a -m 'add npm script entry for testing'
```

We also need an assertion library. We'll use [Chai](http://chaijs.com/):

```shell
npm install --save-dev chai
git commit -a -m 'add chai as the assertion library'
```

### Tools

Our main build tool is [Gulp](http://gulpjs.com/), and although it's not the default release yet, we're going to use version 4.0:

```shell
npm install --save-dev gulpjs/gulp#4.0
git commit -a -m 'add gulp 4.0 as the main build tool'
```

### Node Applications

Now we have the foundation for all of our JavaScript applications, let's create a branch that is more specific, for Node applications:

```shell
git checkout -b node
```

Tell ESLint that Node constructs like `require('module')` are supported:

```shell
cat <<EOF >> .eslintrc.yml
env:
  node: true
EOF
git commit -a -m 'allow node constructs when linting'
```

### Next Steps

I have a bunch of JavaScript projects that follow a variety of patterns, and I'll be using this repo and blog post to keep track of tools and 'best practice' as I come across them, or adopt them.