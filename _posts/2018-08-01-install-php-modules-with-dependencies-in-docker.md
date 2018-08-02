---
layout: post-docker
title:  "Installing PHP Modules with Dependencies in Docker"
date:   2018-08-01 22:58:00 -0700
author: Paul Redmond
permalink: /install-php-modules-with-dependencies-in-docker/
categories:
    - docker
    - php
excerpt: "Learn how to install PHP modules in Docker that have library dependencies such as the intl and zip extensions. We'll walk through each step of installing extensions using the command line and a Dockerfile."
short_description: "Learn how to install PHP modules in Docker that have library dependencies such as the intl and zip extensions. We'll walk through each step of installing extensions using the command line and a Dockerfile."
published: true
comments: false
twtter_card_type: summary_large_image
twitter_image: /assets/images/blog/social-images/install-php-extensions-with-dependencies.png
---

A question I received from a reader recently asks:

> I need the "intl" and "zip" php extensions installed for a project of mine but I can't make it work. I tried running "docker-php-ext-install intl zip"...but had no success.

The official [PHP Docker image readme](https://hub.docker.com/_/php/) has an example of installing PHP extensions using the image's `docker-php-ext-install` script, and demonstrates how to install the underlying libraries using `apt`.

Let's look at it together in this video and see how you can install the `intl` and `zip` extensions in a Docker PHP image, step-by-step.

<div class="media-breakout-lg mt-3 mb-3">
    <div class="video-responsive">
        <iframe width="854" height="480" src="https://www.youtube.com/embed/BBRS-8leLKc" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
    </div>
</div>

## Learn More

If you enjoyed this screencast, I wrote Docker for PHP Developers, a course about building wondrous, flexible, PHP containers.

[Check out my course page](https://bitpress.io/docker-for-php-developers/) to learn more!
