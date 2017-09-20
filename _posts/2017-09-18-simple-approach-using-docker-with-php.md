---
layout: post-docker
title:  "My Simple Approach to using Docker and PHP"
date:   2017-09-17 23:47:00 -0700
author: Paul Redmond
permalink: /simple-approach-using-docker-with-php/
categories:
    - docker
    - laravel
    - php
excerpt: A simple approach to using Docker with PHP and Laravel
short_description: I'm going to show you a straightforward Docker setup that you can use to get started on a new project. Come along with me and give Docker and PHP a shot as I walk you through setting up a Laravel application with an easy Docker setup!
published: true
---

Getting started with Docker and PHP can still be a steep learning curve. You might feel that you don't need something like Docker and that Vagrant or full local development work just fine. I still use local development on some projects because the barrier to entry is small.

You will likely face scenarios where you require different versions of PHP, work with multiple developers, and seek consistency between environments. When you're working with a team, you need a consistent way to develop. I've been on teams where different versions of PHP and MySQL varied between developers. I want to show you how Docker can fill the gap of providing consistent development environments, and do so without a huge amount of added complexity.

## Background

I think a challenge you face as a developer trying to learn Docker, is how overwhelming getting started can feel because of all the options available. In the short four years since Docker was initially released, many tools have been hoisted up to make working with Docker simpler, but I imagine it's hard for a newcomer to figure out where to get started.

I think part of the complexity issue stems from engineers over-engineering things. Using Docker should simplify your application, not make it more convoluted to set up and understand. You don't have to rely on external tools outside of Docker! You want an opinionated setup with conventions that work specifically for your situation, not a pre-baked solution that has everything but the kitchen sink. Keeping your environments as simple as possible given your requirements can drastically improve developer onboarding times. For this discussion, to me, onboarding is how long it takes from handing a new developer a laptop to writing his or her first feature.

In some organizations I've worked with, I've seen onboarding new developers take days instead of hours; and in my opinion, that's just unacceptable. Frequently, long onboarding times indicate inconsistencies between environments and not anticipating production-only issues in development. Long onboarding times can also be a sign of undocumented setup steps that aren't automated or are in the heads of developers.

Ensuring that the same modules and dependencies get installed across all environments is another challenge that's hard to overcome.

Threading all these issues together, Docker has shined for me in overcoming them. I'm running practically the same way at every step of the software lifecycle. New developers can get started on my projects in less than an hour (sometimes in less than ten minutes). From local development out to production, my application environment is quite similar; and sharing applications with others isn't as painful as it once was.

## Getting started

I take a very simplistic approach to using Docker, and I want to show you how you can too—without a bunch of overwhelming cruft. You can build hand-crafted Docker setups without any third party tools that contain what you need and nothing more.

We're going to use [Laravel](https://laravel.com/) as the example application setup here, but you can use anything you want, and it'll be somewhat similar.

I am assuming that you have Docker installed on your machine—it's not that difficult these days. Docker has made it really simple to install on [Mac](https://docs.docker.com/docker-for-mac/install/), [Linux](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/), and [Windows](https://docs.docker.com/docker-for-windows/install/). You will install two important tools that we'll use: the Docker CLI, and [Docker Compose](https://docs.docker.com/compose/).

You will also need to install [Composer](https://getcomposer.org/) and have a recent version of PHP installed locally. Yes, sometimes I just run composer commands locally while I am developing with Docker.

I like to create projects in my `~/Code` folder, but if you want to follow along you can install this project anywhere you want:

```
$ composer create-project laravel/laravel:~5.4 laravel-docker
$ cd laravel-docker
```

There's nothing new so far, just a stock Laravel project.

## Setting Up Docker

I organize my Docker files within my application code directory for each project. In practice, I have a base image that I extend in some of my projects, but I've found that having a base image *isn't always necessary* if my application is simple. [Yagni](https://martinfowler.com/bliki/Yagni.html). KISS.

Here's my basic setup for organizing my Docker files within a Laravel project:

```
├── .docker
│   └── php
│       ├── Dockerfile
│       └── vhost.conf
├── app
├── artisan
├── bootstrap
├── config
├── database
├── public
├── readme.md
├── resources
├── routes
├── storage
├── tests
├── composer.json
├── composer.lock
├── docker-compose.yml
└── webpack.mix.js
```

My setup is similar no matter which framework or application I'm using; I use `.docker/` as *my* folder convention because I like to tuck away the files out of the root of the project. The only file in the root of my project is the `docker-compose.yml` file, which you will learn about later.

Let's create the files we need for Docker:

```
$ mkdir .docker/
$ touch .docker/Dockerfile .docker/vhost.conf
$ touch docker-compose.yml
```

This setup is about as simple as it gets!

We can quickly flesh out the Dockerfile, the Docker Compose configuration, and a simple Apache Vhost. Afterwards, we'll even add Redis and MySQL to the mix with hardly any effort on your part.

Don't worry if you don't know what a Dockerfile does, or how to configure Docker Compose; I'll show you how easy it can be.

## The Dockerfile

The Dockerfile is a set of instructions used for building a Docker image from scratch and containers run instances of those images (i.e., an Apache process, PHP-FPM, etc.). Usually, your Dockerfiles will extend another image; Docker hub has official images for every flavor of Linux imaginable, and you could build your own by extending CentOS or Ubuntu. For PHP I like using the [official PHP images](https://hub.docker.com/_/php/).

On most of my projects, I prefer using [Caddy](https://caddyserver.com/) for the web server with PHP-FPM, and I go over using Nginx and Caddy more in-depth in my upcoming book, [Docker for PHP Developers](/docker-for-php-developers/). However, there's nothing wrong with simplifying things and using the Apache version. That's what we're going to do here!

<script src="https://gist.github.com/paulredmond/6098ba8f259708d0cb023d8af637218d.js"></script>

Let me reiterate that the Dockerfile is a set of instructions. The instruction is the first part of each line in ALL CAPS. 

The **FROM** instruction means we are building our image on top of the tagged official `php:7.1.8-apache` image. Think if this like PHP's `extends` keyword. The image we are extending is giving us a bunch of stuff for free like taking care of installing PHP and Apache and running Apache. We are extending it by defining project-specific needs like PHP modules, files, and server configurations.

The **COPY** instruction is copying our project's files into the "/srv/app" folder. This folder doesn't exist so the COPY instruction creates it too. The second **COPY** is copying our `vhost.conf` file that we haven't written yet, and it will be named `000-default.conf` inside the image; we are essentially overwriting the default Vhost image that Apache will use automatically to server our application. Removing the default ensures that our Vhost is the only one defined and will run as the default when we don't specify a hostname.

The last **RUN** instruction changes ownership of the application files to the Apache `www-data` user, making files writable by the Apache user.

## The Apache Vhost

The Vhost is simple:

<script src="https://gist.github.com/paulredmond/9d70cfdf098b10ae80dcdf474f55ff7f.js"></script>

We define Laravel's public folder with the `DocumentRoot` and we "allow override" so that we can use Laravel's `.htaccess` file for rewrites. You could disable `.htaccess` in this file if you want and add your own rewrite rules to the Vhost file, but we'll keep it simple.

The *ErrorLog* and *CustomLog* files are in the `/var/log/apache2/`, folder. Both of these files are symlinked to stderr and stdout respectively, so you will see their output when you run a container with this image. 

## Building the Image

Docker images are the building blocks of running containers. Think of Docker images as a *PHP class* and a container as an *instance* of that class. The instance gets constructed, code gets executed against it, and finally, an object destructs.

To run a docker container from our image, we need to build it first. Think of a Docker builds as a **single, shippable artifact** for a code release. Deploying a single artifact for a dynamic language like PHP results in **super predictable, reliable, and easy to roll back** builds.

If you are using a CI process to build and deploy images, the image artifact can be pulled down to your local machine, and you have the same thing that's running in production. Having this level of environment parity makes investigating bugs much easier.

With that introduction out of the way, let's build our image!

We will use our Dockerfile to build an image locally, using `docker build` command:

```
$ docker build \
  --file .docker/Dockerfile \
  -t laravel-docker .
```

The first time you run this command, it might take a few minutes because Docker needs to download (pull) the PHP image we are extending. You will see output for each Dockerfile instruction and once the build is complete, the image will be available locally.

The build command we ran used the `--file` flag to tell Docker where to look for our Dockerfile. By default, Docker looks in the same folder in which you are running the command. The `-t` flag is how to name the image, and the final dot (.) means the build will run in the context of the current folder.

The context is important here: Docker uses the given path as the base path for the Dockerfile when we define instructions like "COPY . /srv/app".

Using the `--file` flag allows us to tuck away our Docker setup in the `.docker/` folder, keeping Docker-specific files out of the project root.

Before we run the application, let's visualize the list of images we have locally, including our application image:

```
$ docker images
REPOSITORY      TAG           IMAGE ID      SIZE
laravel-docker  latest        350d3977ef6e  445MB
php             7.1.0-apache. cb73c20d115c  386MB
```

I've omitted the **CREATED** column, but you should see your image listed that we named with `-t laravel-docker` and the PHP image which we extended.

You could remove the laravel-docker image with `docker rmi laravel-docker` if you wanted, but you'd need to rebuild it to run the application. Keep the `docker rmi` command in your back pocket when you need to clean up your local workspace.

## Running Apache

We are ready to run our application with Apache by running a container based on the application image we just built. We can use the `docker run` command to run a container in the foreground:

```
$ docker run --rm -p 8080:80 laravel-docker
```

This command should output some Apache log entries in the terminal, and if you visit [http://localhost:8080](http://localhost:8080) you should see the default Laravel welcome page!

The `--rm` flag will remove the container after we hit `Ctrl+c` to send the shutdown signal to the container. If we omitted the `--rm` flag, you would see the stopped container with `docker ps -a`. The `ps` command shows you the list of containers, and the `-a` flag means "all" containers.

The `-p` flag maps port `8080` on your local machine to port `80` inside the container.

The last argument `laravel-docker` is the name of the image you want to use to run the container.

If you want to see the running laravel-docker container in the Docker CLI, open a new terminal window and run `docker ps`:

```
$ docker ps
CONTAINER ID  IMAGE           STATUS        NAMES
1de66149b26c  laravel-docker  Up 2 seconds  determined_banach
```

The NAMES column is a generated container name because we didn't pass a particular name. We could run the command like this to specify a name:

```
$ docker run \
  --name laravel-app \
  --rm -p 8080:80
  laravel-docker 
```

Without much effort, we already have a running application. We will expand on some things, but I hope you can begin to see that you **don't need a ton of magic** around your Docker setup.

## Running With Docker Compose

We just ran the application with `docker run`, but if you try to modify a file locally, you'll notice that the changes don't take effect in Docker. We could define some volumes in the `docker run` command to start developing, but doing so wouldn't produce the beautiful repeatable development environment we are after. Instead, we're going to set up Docker Compose now to improve our workflow and make it repeatable for other developers.

Docker Compose automates running your containers, linking them together, and networking them. We can define our application and then link up MySQL and Redis quickly to build on our Laravel setup.

Let's start by creating the equivalent structure to match what we were doing with `docker run`. Open up the `docker-compose.yml` file and add the following:

<script src="https://gist.github.com/paulredmond/2149d1c97d35b6ca73e4260ab56cf691.js"></script>

You might notice some similarities to the commands we've already been running. The build context is `.`, the Dockerfile is pointing to `.docker/Dockerfile`, and the image name is `laravel-docker`. Last, we map port `8080` to port `80` inside the container.

Make sure that you shut down the existing container if you already have one running, and then run this command in the root of your project:

```
$ docker-compose up --build
```

I showed you the build flag here, because you will need to build the image on a new machine before running it and when you make changes to the Dockerfile or Vhost. You can also run `docker-compose build` to build the image and then `docker-compose up`.

You should see the container running in the foreground, and you should be able to see the same welcome page if you open [http://localhost:8080](http://localhost:8080) in your browser.

You can start to see the benefit of Docker Compose, especially as you start adding services like MySQL and Redis. Using `docker run` starts to break down quickly. In fact, let's look at other automated things we can add to the Docker Compose file.

## Adding a Volume for Local Development

We can add a volume to our Docker Compose file to make changes to the source code and have the changes be reflected immediately in the container. Having your changes reflected immediately is a must for your development workflow. Otherwise, you'd have to rebuild the Docker image with every code change you want to test.

The Compose file has a `volumes` key that we can add:

<script src="https://gist.github.com/paulredmond/de59edf6a42b8c3fbf90711ca7535523.js"></script>

The `volumes` key allows you to specify a path on the host machine (i.e., your laptop) that maps to a path in the container. In this case, we will mount the root of our project to the `/srv/app` folder, which is where Apache will be looking for our code.

The image build already has our code inside of it, but when you run a container with a volume, **the local copy takes precidence**. 

You'll need to shut down application if it's running in the foreground, make the change above to the `docker-compose.yml` file, and then start the application again to pick up the volume change. You run your container by running `docker-compose up` again.

After you start your container back up, let's verify that the volume is working by adding the following in `public/index.php` file locally:

```
<?php

phpinfo(); exit;
```

With your container running, you should see the output from "phpinfo()" in your browser. If you revert the change, you should see the default Laravel welcome page again.

Now you can make changes while your project is running without restarting or rebuilding the image. If you notice that your machine has any file permission errors, you might need to execute the following in a Laravel project locally:

```
$ chmod -R o+rw bootstrap/ storage/
```

Only make this change in a local environment, but basically, this will allow the `www-data` user to write to these directories.

## Adding MySQL

Before we wrap up, let's add [MySQL](https://hub.docker.com/_/mysql/) and [Redis](https://hub.docker.com/_/redis/) to the mix. 

To use MySQL, we also need to install the *pdo_mysql* extension, because the PHP image doesn't include it by default. The official PHP image comes with some helper scripts like `docker-php-ext-install` that we can use to install extensions during a `docker build`.

Before we update the Dockerfile though, let's define the additional services in the `docker-compose.yml`:

<script src="https://gist.github.com/paulredmond/4c16f4308a8a1a1516cea083437d789f.js"></script>

There are quite a few new things here, but nothing too complicated with a little explaining. We've added the MySQL and Redis services, referencing the image tags (mysql:5.7 and redis:4.0-alpine) we want to use from Docker Hub. Using specific versions is a good idea to avoid unexpected changes, just like properly versioning your PHP Composer dependencies.

If you check the [MySQL Docker](https://hub.docker.com/_/mysql/) readme, the image defines some environment variables for creating a database, the user, and the root password. In our Docker Compose file, we define these environment variables, and then add some environment variables to the `app` service, so Laravel is configured correctly to communicate with the MySQL and Redis services.

We use the `links` key in the app service so that the application can communicate with these containers through the `redis` and `mysql` hostnames. Docker Compose networks these services for us, and the network hostnames match the keys for the service. For example, `DB_HOST: mysql` replaces the default `DB_HOST: 127.0.0.1` value in Laravel's .env file.

We also configure Laravel to use Redis for sessions and cache through environment variables, which will override values found in the `.env` file.

And now, the Dockerfile adjustments to go along with our new services so we can communicate with them:

<script src="https://gist.github.com/paulredmond/169d0e1db35ec2b8996bc29e8c596c34.js"></script>

We define the *WORKDIR* as */srv/app* so that your commands will run relative to that path, and when you enter the container with bash (I will show you this in a minute), you will automatically start in the `/srv/app` path.

Next, we updated the RUN instruction to install `mbstring`, `pdo`, and `pdo_mysql`, so we can use MySQL with our application.

Also, to use Redis with Laravel, we need to install the `predis/predis` composer package:

```
$ composer require predis/predis
```

If you stop your containers and then run `docker-compose up --build` you should see the Redis and MySQL services starting in the Docker Compose output after your updated build finishes.

## Running Commands

With your containers running the application, open another tab so you can peek around inside the container and run a few commands.

Here are a couple of ways you could to that:

```
# Using docker-compose
$ docker-compose exec app /bin/bash
root@4c1ee740cb92:/srv/app#

# Using Docker
$ docker ps
CONTAINER ID        IMAGE
4c1ee740cb92        laravel-docker
ba094abe8c85        redis:4.0-alpine
6dce0efa2dc6        mysql:5.7

$ docker exec -it 4c1ee740cb92 /bin/bash
root@4c1ee740cb92:/srv/app#
```

We are running a bash session in the application container. Docker Compose has a shortcut command, or you can use `docker exec`. The `-i` flag keeps STDIN open even if not attached, and the `-t` flag allocates a pseudo-TTY to give you a text-only console to run an interactive bash shell. You can kind of think of it like running SSH if that helps make sense to you.

You could also run the above like so:

```
$ docker exec -it 4c1ee740cb92 bash
```

I showed you `/bin/bash` so you could understand that we are executing a command, and since bash is in the *path* you can just reference it without the full path.

Now that you know how to run bash commands in the running container, let's migrate the database:

```
$ php artisan migrate
Migration table created successfully.
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table
```

Now you have a working database for development!

You can run all your artisan commands here. You can also connect to MySQL (and Redis) locally, which we will cover before we wrap up.

## Connecting to MySQL Locally

While the container is running, you can connect to MySQL from your local machine with a tool like [Sequel Pro](https://www.sequelpro.com/) on the Mac, or even just the `mysql` client on the command line.

If you recall, the `docker-compose.yml` file defined a port map of `13306:3306`, and you can connect with the `13306` port on your local machine. If you are wondering why I picked `13306`, mainly, I just add a `1` in front of the original port or `3306 + 10,000` if you want to get technical. I map ports like this in Docker because I still like to have a local PHP and MySQL setup on the standard ports. This port is arbitrary, but adding a `1` makes it easier for me to remember without looking it up.

To connect to MySQL from the CLI, you would run the following:

```
$ mysql -u root -P 13306 -h 127.0.0.1 -p
Enter password:
Server version: 5.7.18 MySQL Community Server (GPL)
...
show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| laravel_docker     |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.01 sec)

mysql>
```

You can keep your standard MySQL workflow with a GUI, which I find convenient.

## Upgrading with Docker is Great

Imagine that you are using Homestead or even Valet to run your applications. When new versions of PHP come out, you have to upgrade your entire development environment. Using Docker, you can update your project immediately without affecting your whole workflow with other projects.

I love that my runtime environment is close to my application code. If I want to try out a new version of PHP with my application, I make a small change in my Dockerfile, build it, and run my tests. Changes in my environment don't affect my other projects.

If you build a base image for all your projects, you can update one project tag independently without affecting other applications. You can start rolling out updated versions across the board and have more confidence that your infrastructure won't break down. If it does, then you roll back and ship the previous tag until you fix it.

## Wrap Up

Consider this my manifesto to advocating for simple Docker setups and not trying to over-engineer things. The adoption rates of Docker at large and medium-sized companies are staggering. I would argue that most development shops are at least considering trying it out. My goal is to help PHP developers choose Docker by making getting started as simple as possible.

Some might criticize how simple this setup is, but I think it proves that using Docker **doesn't have to be convoluted and confusing**. We only needed three files to get our application environment up and running. Of course, the complexity of your setup is going to depend on the application you are building, which dependencies you have, etc. But with only a few configuration files, we have a decent development environment, and I would argue that it's easy to follow.

I like using the official PHP image and other official images whenever possible over rolling my own. The PHP image makes things simple for most of my use-cases because all I need to do is focus on installing modules, not PHP itself. I imagine some extensions might be complicated to install with the PHP image, but **with that complexity comes overhead**. I like to avoid as much cost as possible unless it makes sense.

To build upon this image, you could also run PHPUnit from inside the container at this point with a little work. I'd recommend installing the SQLite extension and run unit tests with an in-memory database. Feel free to try it out on your own and let me know if you run into any issues. I'd be glad to help out!
