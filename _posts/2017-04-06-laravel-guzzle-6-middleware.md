---
layout: post
title:  "Using Guzzle 6 Middleware in a Laravel Application"
date:   2017-04-06 22:14:00 -0700
author: Paul Redmond
categories: laravel php
excerpt: Using Guzzle 6 Middleware in a Laravel Application
short_description: I want to show you some of my techniques for defining reusable middleware and other tricks that will hopefully help you work more comfortably with Guzzle 6 in a Laravel application. These techniques also apply outside of the Laravel framework.
published: true
---

The most significant change between Guzzle 5 and 6 is moving away from the [event system I grew so accustomed to in Version 5](http://docs.guzzlephp.org/en/5.3/events.html) to [middleware](https://github.com/guzzle/guzzle/blob/master/UPGRADING.md#migrating-to-middleware) in version 6. Needless to say, it was a big adjustment for me at first and it felt like a downgrade. After my initial grumbling, the upgrade guide explains the reasoning for the change:

> Instead of using the event system to listen for things like the before event, you now create a stack based middleware function that intercepts a request on the way in and the promise of the response on the way out. This is a much simpler and more predictable approach than the event system and works nicely with PSR-7 middleware.

I prefer to keep my dependencies as up-to-date as possible so I decided to learn Guzzle 6 and become more familiar with the middleware. The concepts are pretty straightforward and I have a few patterns that I like to use when building out middleware within my [Laravel](https://laravel.com/) applications.

I actually had a real need to build out [HMAC](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code). The following code illustrates how you might go about building HMAC authorization by sending a signed `Authorization` header with a Guzzle middleware:

<script src="https://gist.github.com/paulredmond/8a974779d11d6e9950be2d73805e8115.js"></script>

The middleware uses PHP's [`__invoke` magic method](http://php.net/manual/en/language.oop5.magic.php#object.invoke) and includes a couple service dependencies that can come from a service container, configuration, etc. With this middleware, Guzzle might send something similiar to the following header:

```
Authorization: Signature keyId="key",algorithm="hmac-sha1",signature="za3U%2BbsQX4otneqSfYZuJuZP%2FrY%3D"
```

Next, you can resolve tagged middleware with Laravel's service container. We will define these services within `\App\Providers\AppServiceProvider`:

<script src="https://gist.github.com/paulredmond/509fdbf46d97c6d09498d246082f439f.js"></script>

In a real application, you would pass configuration values into the middleware constructor, but `key` and `secret` are just to simplify the example. To experiment with your new middleware, create a simple route in `routes/web.php` that you can send requests to:

<script src="https://gist.github.com/paulredmond/cbca443b537dae035d6c8bfb737499f3.js"></script>

Now try it out with `php artisan tinker`:

<script src="https://gist.github.com/paulredmond/6f80fe5aa0fcbc1b3ef83272bea8445d.js"></script>

Boom! This is a simple example, but you could also tag multiple groups of middleware that you can mix and match within various HTTP clients!
