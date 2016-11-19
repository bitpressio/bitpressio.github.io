---
layout: post
title:  Docker for PHP Developers Book Announcement
date:   2016-11-19 02:00:00 -0700
author: Paul Redmond
categories: php docker
excerpt: We are excited to announce an upcoming book! We are hard at work on a new book titled "Docker for PHP Developers".
published: true
---
I am excited to announce our next book title at Bitpress, [Docker for PHP Developers](https://leanpub.com/docker-for-php-developers)! Docker has grown on me over the last two years, but I realize that not all PHP developers have experimented with it yet. Google produces lots of tutorials and projects using Docker with PHP, but you will be hard-pressed to find a complete guide that takes you from __beginner to using Docker full time every day__.

I want to change that.

<span class="pull-left" style="display: block; margin-right: 20px;">
[![Docker for PHP Developers Book](/assets/images/blog/docker-for-php-developers-cover.png)](https://leanpub.com/docker-for-php-developers)
</span>

The goal of this book is to __provide you with answers to most every common challenge you will likely run into when you get started with Docker__. By the end of the book, you should be able to create a reusable Docker environment to base all your projects on and stop installing PHP modules by hand on your local machine, or stop using a bloated virtual machine for development.

Is Docker a silver bullet? No. I still argue that nothing is more productive for me than working locally; but developing locally isn't realistic when you work with a team and have multiple environments in which your application is expected to work.

No more "it works on my machine", because local, staging, and production will all be nearly identical.

If you already use Docker, perhaps you will learn a few tricks along the way, and hopefully help me make a better product for those who are just starting out on their docker adventure.

[Docker](https://www.docker.com/) has been my primary development environment over the last couple of years, and I've learned some useful tricks and tips on how to make Docker part of my primary development workflow that you will benefit from.

I've cut my teeth along the way and refined my approach with each new project. Docker was definitely a new way of thinking that I had to wrap my head around, and I'd venture to guess that most developers struggle with its concepts at first.

<span class="clearfix"></span>

I don't feel my workflow perfect, but my Docker workflow with PHP satisifes some requirements I think are very important to my productivity:

* Make my Docker workflow feel (almost) the same as developing locally
* Modify files locally that are immediately reflected in my application environment
* Install development modules (XDebug) but leaving no trace of them in production builds
* Making reusable patterns that I can use on every project
* Installing private Composer/Git Packages within a Docker build (without using SSH keys)
* Cache Composer dependencies for faster builds
* Run tests within Docker during development and in a Continuous Integration (CI) environment
* Customizing PHP's environment configuration for each project
* Using the same build process for production and development
* A repeatable development environment, including both code and data, that makes it easy for new developers to get started quickly

This is not an all-inclusive list of things I've learned along the way, but some of the more important ones. I am sure there's more I am forgetting.

Please share your questions and concerns with your own Docker workflow in the comments. I would love your feedback to go into the final product. Also, make sure and [show your interest in the book](https://leanpub.com/docker-for-php-developers) so we can notify you when its ready!
