---
layout: post
title:  "How to Create Automated Documentation for Atom Text Editor Snippets"
date:   2017-02-13 22:11:00 -0700
author: Paul Redmond
categories: php laravel
excerpt: While creating snippets for an Atom package, I created a snippet documentation generator for human-friendly snippet documentation.
short_description: Learn how to generate markdown documentation from your Atom plugin snippet files (CSON). This is a quick writeup of how I automated the process of generating documentation from the source CSON files.
published: true
---

While [building an Laravel snippet plugin](/php/laravel/2017/02/13/larasnippets-atom-plugin/) for the [Atom text editor](https://atom.io/), I thought it would be helpful to produce human-friendly documentation for all the snippets contained in my plugin. This post is a quick tutorial demonstrating how to parse CSON files and produce documentation for your Atom plugin.

Atom's [snippets](http://flight-manual.atom.io/using-atom/sections/snippets/) use CSON (CoffeeScript-Object-Notation), which is a readable enough format, but I wanted to combine all snippets into a generated, human-readable markdown document.

You can see the [docs.js](https://github.com/paulredmond/atom-larasnippets/blob/master/docs.js) script in my [Larasnippets plugin](https://atom.io/packages/larasnippets):

<script src="https://gist.github.com/paulredmond/31e5b656a2bc63b93a2d3d939ecd38d8.js"></script>

This script defines a Snippet object, and then loops through all `.cson` files. The [cson module](https://www.npmjs.com/package/cson) parses each snippet file into an `Object` which is then pushed into an array of Snippet objects. Finally, I used lodash to templatize the documentation block and loop through the snippets.

You can check out the example [markdown output](https://github.com/paulredmond/atom-larasnippets/blob/master/doc/snippet-reference.markdown) generated from the Node.js script script.
