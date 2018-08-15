---
layout: post
title: "V is for Versions"
comments: false
tags:
  - A to Z 2013
published: false
---

A quick one today, something to bear in mind when using third-party software in your websites.

I was tasked with adding CMS functionality to one of our sites to allow some of our staff to edit the content directly. Did some searching and found that ModX fit the bill nicely. Installed it on a test server, played with it a bit to make sure it would do what we wanted, all was good. As part of the evaluation process I started to add one of the more complicated features that would be needed. It worked fine, so the next feature got added. Before I knew it, the site was done, ready for deploy. A rare occasion where everything went fine.

Until…

Another developer pointed out that I’d installed the latest dev branch of ModX during my initial assessment. For the uninitiated, programs generally come in two flavours, stable and dev. Stable is “everything is fine, everything works, build your website”. Dev is “we’ve added some cool new features, it should work, but don’t use it on a live site, just in case”.

Cue two days of installing the earlier stable package and converting weeks of code to the older, but stable, version.

So always make sure you’re on the right release path before going too far, or you’ll look like a bit of an idiot in front of your team…
