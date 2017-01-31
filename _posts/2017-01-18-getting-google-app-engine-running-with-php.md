---
layout: post
title: "Getting Google App Engine running with PHP"
comments: true
excerpt: "If you want to get up and running with GAE and PHP, I've created a basic Vagrantfile to get you as far as your first app deploy to the cloud."
category: php
tags:
  - Google App Engine
---

Herein you'll find instructions on getting Google App Engine running with PHP using vagrant. It's not the most elegant way, but it works.

## Background

In my new job, we use GAE a fair bit. Seemed only prudent that during the interview process, I learn the basics. The [Hello World tutorial](https://cloud.google.com/appengine/docs/php/quickstart) is pretty straight forward, but I just could not get it to run.
Issues with python versions and guessing the right PHP version lead to a dead end, even [Google staff accepted there was a problem](https://stackoverflow.com/questions/39993518/unable-to-get-google-app-engine-php-tutorial-working). Seemed to work for most people just not for me...

I gave up on it as a bad do, but having gotten the new job it wasn't long before I was tasked with a project that uses GAE. So I hit it with fresh vigour, and after trawling the documentation spotted the flaw. Such a simple flaw. One that could be fixed by renaming an argument name.

Change `--php_executable_path` to `--php_cgi_executable_path`

All the time I'd been directing GAE to use the regular PHP runtime, when it needed the CGI variant. I changed the launch script over and low and behold, it worked!

I trawled through my notes and history and turned it into a Vagrantfile so I could create a working GAE dev environment easily. And now you can too!

## Requirements

* [Install VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Install Vagrant](https://www.vagrantup.com/docs/installation/)
* [Install Git](https://www.atlassian.com/git/tutorials/install-git/)
* [Create an account for Google Cloud Services](https://appengine.google.com/start)
* [Create a project](https://console.cloud.google.com/iam-admin/projects)

## Instructions

* Grab a [copy of the repository from GitHub](https://github.com/chrisarmitage/gae-php-hello-world)<br />
  `git clone git@github.com:chrisarmitage/gae-php-hello-world.git`

* Create the vagrant box<br />
  `cd gae-php-hello-world`<br />
  `vagrant up`<br />

* Log in to the box<br />
  `vagrant ssh`<br />

* Set up your Google Cloud credentials<br />
  `gcloud init`<br />
  
* Use the basic launcher script<br />
  `cd /vagrant/applications/hello-world`<br />
  `./launch-gae.sh`<br />

* View the application running in development<br />
  `http://192.168.33.30:8080`<br />

And you should see a wonderful page welcoming you to the world of Google App Engine! If not, drop me a line below and I'll do what I can to help.

Now that we've got it running locally, let's move on to your first app deploy.

## Deploying the app

Once you have the dev server running and your account credentials are working, deploying is pretty easy.

* If you haven't already, shut down the dev server<br />
  Press `ctrl-c`<br />

* Trigger a deployment<br />
  `gloud app deploy`<br />
  (you may need to set a region depending on how your account is set up)<br />

Give it around 60 seconds and you'll get confirmation that your app has deployed. Go to the link it tells you, and you'll see the PHP Info page again.

And that's it! You've just deployed your first PHP application to the Google App Engine.

I'll be putting together some more tutorials / posts to do with the various features of GAE, but in the meantime have a poke around.

Note: If you pay attention to the local and live PHP Info pages, you'll notice a few differences.

* GAE currently runs PHP 5.5. Yes, the one that went [End of Life back in July 2016](http://php.net/eol.php). 5.5 is no longer available in Ubuntu 16.04 (rightly so) so we use 5.6 locally. This will cause issues if you use any 5.6 features in your code. I'd recommend setting the [PHP Environment in PhpStorm](https://www.jetbrains.com/help/phpstorm/2016.3/enabling-php-support-in-a-project.html) to help reduce the risk.
* The modules may be slightly different. I've tried to get the modules as close as I can, but it needs some further tweaking. Primarily, installing php-memcache locally breaks things. I'm planning on addressing these in further posts when we actually use the functionalities.
