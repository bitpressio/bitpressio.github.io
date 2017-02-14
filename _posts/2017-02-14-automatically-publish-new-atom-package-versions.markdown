---
layout: post
title:  "Automatically Publish New Atom Package Versions"
date:   2017-02-14 16:58:00 -0700
author: Paul Redmond
categories: atom
excerpt: Automatically publish new versions of your Atom text editor packages
short_description: Automatically publish new versions of your Atom text editor packages with a simple bash script that takes care of incrementing the NPM package version, pushing tags to the remote repository, and publishing the new version with APM (Atom Package Manager)
published: true
---

I wrote this quick bash script that automatically increments the version of my Atom text editor plugins, pushes everything to my git remote, and then finally publishes the new version to Atom via [apm](https://github.com/atom/apm):

<script src="https://gist.github.com/paulredmond/9dbae7e6c83b98db3a369e181acea1bc.js"></script>

Drop this file in the root of your Atom package (see [my example](https://github.com/paulredmond/atom-larasnippets/blob/master/publish)) and make sure the file is executable with `chmod u+x ./publish`. In my example, I also generate the [latest snippet documentation](http://bitpress.io/php/laravel/2017/02/14/atom-cson-snippet-documentation-generator/) before incrementing a new version, pushing tags, and finally publishing the new version with APM.

You can also publish different types of releases, for example, the following would publish the next major version of your package:

```
./publish major
```

The default is `minor` and you can run `./publish`. When you fix bugs, you can publish those versions with `./publish patch`. See `npm version --help` for more info about [semantic versioning](http://semver.org/). Cheers!
