---
layout: post
title:  "How to Accept JSON POST Requests in Lumen"
date:   2016-02-16 12:14:46 -0700
author: Paul Redmond
categories: php laravel
excerpt: Learn how to accept a JSON payload in your Lumen API with a middleware
published: true
---
Sending a JSON encoded entity to a RESTful API is a common need, and a JSON payload is actually pretty slick and convenient. It eases the pain of form-encoded strings when sending post data, and makes it easier to structure complex data that’s more readable. It can also make documentation easier to follow when you share cURL examples of making requests to your API. JSON is much easier for readers to digest compared to form encoded request params.

So how do we do it in Lumen? I will show you a couple ways we can read JSON data and a middleware that can make it easy to support both `Content-Type: application/json` as well as the traditional `Content-Type: application/x-www-form-urlencoded` without polluting the controller with content-type specific logic.

To illustrate, let’s say you are accepting a POST request to create a new blog post in your API and allowing JSON-encoded requests:

{% highlight bash %}
curl http://example.com/posts \
  -d '{"title": "My First Post!","body": "This is my first blog post!"}' \
  -H 'Content-Type: application/json'
{% endhighlight %}

To read the data in a Lumen route closure/controller, you can use the Illuminate\Http\Request::json() method like so:

<script src="https://gist.github.com/paulredmond/2e4cb372adfb5f28997f.js"></script>

This approach works and you can easily start accepting a JSON payload, however, I prefer a different approach to remove the assumption that only JSON is supported, or worse yet, checking in the controller:

<script src="https://gist.github.com/paulredmond/e37ca72bf32c0dc365a8.js"></script>

We can handle this more elegantly with a middleware:

<script src="https://gist.github.com/paulredmond/cd42c0d044dbdd5263cf.js"></script>

Now you can get all request data in the same way regardless of how the data was submitted:

<script src="https://gist.github.com/paulredmond/65669420bff6649a77a5.js"></script>

Don’t forget to configure the middleware as a global middleware!

<script src="https://gist.github.com/paulredmond/64f967901ecc3e8a778f.js"></script>

Now the middleware will run before each request and and populate the request with JSON data in the same way you’d expect to accept normal POST data!
