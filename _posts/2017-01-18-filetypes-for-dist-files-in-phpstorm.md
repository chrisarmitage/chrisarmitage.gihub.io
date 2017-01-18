---
layout: post
title: "Filetypes for .dist files in PhpStorm"
comments: true
excerpt: "A quick post for handling .dist file in PhpStorm"
category: php
---

For the uninitiated, the likes of phpunit and phpcs can have two types of config file in your project, `phpunit.xml` and `phpunit.xml.dist`. The tool will use `.xml`, or '.xml.dist' if the former isn't present.
The idea is you version-control the `.xml.dist` and version-ignore the `.xml`, so you have project-standard settings, but a developer can overwrite them should they wish (disabling coverage to speed up tests is my most common case).

I've always just tagged the `.dist` as XML files within PhpStorm to get syntax highlighting. And it worked great, until today. I'm using [Humbug]() (mutation testing) more in my development, and it has a `humbug.json.dist` file available. Which immediately kicked off with Unknown Token errors.

What I should have done, is go into File > Settings > Editor > File Types, and added `*.xml.dist` to the XML entry and `*.json.dist` to the JSON one.

All too easy to forget a filetype doesn't have to be just a single extension.
