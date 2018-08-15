---
layout: post
title: "G is for Graphite"
comments: false
category: devops
tags:
  - A to Z 2013
published: false
---

> Note: G was supposed to be GD, the PHP graphics library. When I started writing it up I realised it would be a much longer article than the challenge is aimed at. I’ll get it written up for the future.

Graphite is a metric logging system. In English, it records numbers and puts them into a graph for you. While it was designed to monitor stats relating to software, I’m already using it for business needs, recording information that is of interest to managers, not the dev team.

Setting up a server takes about 30 minutes, another five to get the logging code in to your PHP script, and you’re logging.

As an example of what you can record, we do web service call lengths (to spot any sudden increase in execution time), occurrences of server errors (five second glance to see if our servers are OK), page generation times (to see if any alterations have had an adverse impact) and failed login attempts (a spike is a sure sign we’ve had an attack).

It’s really a case of what you want to record, and dropping in a couple of lines of code. I aim to go more in-depth with this at a later date, with proper info on how we’re up and running (once I get us up and running properly).
