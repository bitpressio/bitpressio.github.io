---
layout: post-docker
title:  "Nginx Unit with Docker, PHP, and Laravel"
date:   2017-09-22 7:00:00 -0700
author: Paul Redmond
permalink: /nginx-unit-laravel-demo/
categories:
    - docker
    - laravel
    - php
excerpt: Learn about the upcoming Nginx Unit
short_description: Learn about the upcoming Nginx Unit web application server and take it for a spin with Docker, PHP7, and Laravel.
published: true
comments: false
---

As part of the announced [Nginx Platform](https://www.nginx.com/products/), Nginx is working on a dynamic web application server, [Nginx Unit](https://www.nginx.com/products/nginx-unit/). Unit supports fully dynamic reconfiguration using a RESTful JSON API, supports multiple application languages and versions can run simultaneously. Right now Unit supports Python, PHP, and Go; with planned support for JavaScript/Node.js, Java, and Ruby.

> NGINX Unit is a dynamic web application server, designed to run applications in multiple languages. Unit is lightweight, polyglot, and dynamically configured via API. The design of the server allows reconfiguration of specific application parameters as needed by the engineering or operations.
>
> NGINX Unit is currently available as a beta. As such, it is suitable for use in a testing environment but is not recommended for use in production.

Unit is beta right now, so I decided to start experimenting with it for PHP applications. You can check out my [Nginx Unit Demo repository](https://github.com/paulredmond/nginx-unit-demo), which runs a Laravel application with Nginx Unit in Docker.

While researching, I decided to use the [ubuntu:xenial](https://hub.docker.com/_/ubuntu/) image to make things easier instead of building from source. Here's the Dockerfile for reference:

<script src="https://gist.github.com/paulredmond/0eb7494b37cb63acd643a8ca2b3edaca.js"></script>

One of the exciting features of Unit for me was the ability to run multiple language configurations (i.e., a Golang and PHP application) and how you define the configuration objects through an API. If you check out my repository, after the Docker container starts, I run the following to define the PHP application:

<script src="https://gist.github.com/paulredmond/72f968c1e297ac19928b5cbd9536ad8e.js"></script>

And here's my demo configuration object for the Laravel application:

<script src="https://gist.github.com/paulredmond/130ca521052ed4cf2415d286063136c7.js"></script>

Take me repo for a spin and let me know what you think [@paulredmond](https://twitter.com/paulredmond)!