---
layout: post-docker
title:  "How to use Caddy with PHP and Docker"
date:   2017-10-11 20:00:00 -0700
author: Paul Redmond
permalink: /caddy-with-docker-and-php/
categories:
    - docker
    - caddy
    - php
excerpt: How to use the Caddy web server with Docker and PHP
short_description: I have found Caddy to be my go-to server for running PHP Docker containers in production. Using the official PHP-FPM image, we can run Caddy and start PHP-FPM in the background within the same container.
published: true
comments: false
---

Caddy is an excellent HTTP/2 web server with automatic HTTPS. The configuration is simple and approachable, providing secure defaults and self-contained binaries that run on every platform.

I have found Caddy to be my go-to server for running PHP Docker containers in production. Using the official PHP-FPM image, we can run Caddy and start PHP-FPM in the background within the same container.

I find that running PHP-FPM and Caddy together simplifies my stack by eliminating the need for FastCGI network communication between something like Nginx and PHP-FPM. 

## Setting up a Project

Using [Laravel](https://laravel.com/), here's how I like to organize my Docker setup:

```
$ cd ~/Code/
$ laravel new docker-caddy

$ mkdir docker/
$ touch docker/Caddyfile
$ touch docker/Dockerfile
$ touch docker-compose.yml
```

I like to create a `docker/` folder to contain my Docker-specific code, and sometimes I even break it out into subfolders like `docker/php/` depending on the needs of my project. In this case a `docker-compose.yml` file to orchestrate containers in development, a `Dockerfile` to build the image, and a `Caddyfile` to configure the web server. 

## The Caddyfile

The Caddyfile is a text file that configures how Caddy runs. The syntax is clean and simple, often requiring a one-liner to enable features with practical conventions.

Here's a typical Caddyfile for a Laravel project:

<script src="https://gist.github.com/paulredmond/d406b956babfc6ec560a633adc3a9571.js"></script>

In production I usually terminate SSL in the cloud provider I am using, and Caddy runs on either its default port of `2015` or `80`. In the Caddyfile above, this server will respond to `0.0.0.0:2015` because no port was specified.

The `gzip` directive is a simple one-liner for enabling gzip with reasonable defaults. You can get more [granular control](https://caddyserver.com/docs/gzip#examples) if needed.

We use the `fastcgi` directive to proxy to respond on the `/` request path.  The `php` on the end of the directive defines some presets for the directive:

```
ext   .php
split .php
index index.php
```

The `rewrite` directive is rewriting all URLs to point to an `index.php` file, which is our `public/index.php` file for Laravel.

The last chunk of code sends all logs to `stdout` which is what we want in Docker, and the server logs will show up in the console.

Last, the `on startup` event starts PHP-FPM in the background so that Caddy can communicate with PHP.

If you haven't used Caddy, an excellent place to start is the [Caddyfile tutorial](https://caddyserver.com/tutorial/caddyfile) which does an excellent job walking you through how to configure  Caddy.

## The Dockerfile

Here is the full Dockerfile located at `docker/Dockerfile` in the project:

<script src="https://gist.github.com/paulredmond/bd28128b8d6684c527e8ed70aedc8e93.js"></script>

The Dockerfile is extended from the official PHP-FPM image, and then we install the Caddy binary from the official build server with the personal license. We make the `caddy` bin executable and install a few PHP extensions.

Next, we copy the source code and Caddyfile and make sure that the `www-data` user owns the application files in `/srv/app`.

The last line provides defaults for an executing container, which will be running caddy with the `/etc/Caddyfile` configuration, and logging output to `stdout`.

Note that we are installing a few plugins `http.expires,http.realip`, and using `license=personal`. If you are using Caddy for a commercial project, you will need a license or build Caddy from source yourself.

## Try it Out

Let's run our Caddy and PHP-FPM image using Docker Compose! Here's what a simple `docker-compose.yml` file would look like:

<script src="https://gist.github.com/paulredmond/5bab25fa7c182df16f19cceb838adc68.js"></script>

We can build the image and run it with one command:

```
$ docker-compose up --build
```

Now if you visit [http://localhost:8080](http://localhost:8080) you will see the Laravel welcome page and Caddy logs in the console!

## Learning More

I like using Caddy because we can combine running PHP-FPM and the web server in the same container. Combining both in one container removes the need for networking between the web server and PHP-FPM. There's nothing wrong with either approach, but I prefer less moving parts.

I recommend learning more about Caddy by reading through the [guide](https://caddyserver.com/tutorial), specifically the [Caddyfile tutorial](https://caddyserver.com/tutorial/caddyfile).

Caddy is open-source, but if you want to use the build server like we're doing in the `Dockerfile` for a commercial purpose, you will need an [official license](https://caddyserver.com/products/licenses). You can also build Caddy yourself, but the plugin build system is full of useful plugins and will save you a ton of time.