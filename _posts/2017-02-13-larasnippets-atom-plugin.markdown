---
layout: post
title:  "Larasnippets: My Collection of Laravel and PHP Snippets for the Atom Editor"
date:   2017-02-13 16:51:00 -0700
author: Paul Redmond
categories: php laravel
excerpt: Larasnippets is my Atom plugin for Laravel and PHP Snippets
short_description: Over the weekend I published an Atom editor plugin for my Laravel and PHP snippets
published: true
---

I've been using the [Atom text editor](https://atom.io/) lately, which is getting really solid. I decided to port some of my Laravel and PHP snippets into an Atom package
to get a feel for how to create packages and snippets in Atom. I am pleased with how easy it was to get started. I just typed `Command + Shift + P` and selected `Package Generator: Generate Package`. Specifically, check out [Hacking Atom](http://flight-manual.atom.io/hacking-atom/) in the [Atom flight manual](http://flight-manual.atom.io/) to get started.

<span class="pull-left" style="display: block; margin-right: 20px;">
[![Larasnippets Atom Package](/assets/images/blog/larasnippets-factory-example.gif)](https://github.com/paulredmond/atom-larasnippets)
</span>

I don't plan on making an exhaustive list of snippets for Laravel; I just have a hand-full I go for that I use often. Since I write tests often, it makes sense for me to tab complete `test` and generate a new PHP unit test. I can also generate a new test case with `testcase` that imports Laravel's migration traits. While I still bounce between PhpStorm and text editors, Atom is really starting to feel polished.
