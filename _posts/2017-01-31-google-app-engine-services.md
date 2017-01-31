---
layout: post
title: "Google App Engine: Services"
comments: true
excerpt: "Services (previously called modules) allow you to break your application into separate chunks, allowing for different frameworks or even different languages within one project."
category: php
tags:
  - Google App Engine
---

Herein you'll find instructions on creating multiple services, allowing you to break your application into smaller, (hopefully) more manageable parts.

## Background

Gone are the days of single monolithic codebases (unless, ya know, you work for an actual company with an actual codebase...). The microservice movement has tens- if not hundreds- of Single Responsibility applications, each doing only one thing.
Even just breaking up code into a web service with separate API and admin services goes a long way to making cleaner, more manageable code.  

## Requirements

* [Have a GAE environment up and running]({% post_url 2017-01-18-getting-google-app-engine-running-with-php %})

## Instructions

Services are declared by creating an additional `app.yaml` file. The syntax is the same, there's only one key change to make.

Let's start with a single service (as we have done in previous tutorials)

`> app.yaml`

```yaml
runtime: php55
api_version: 1

handlers:
- url: /.*
  script: app.php
```

Note: We've changed the `- url:` value to `/.*`. That will match any request and route it to the script.

`> app.php`

```php
Using the default service to handle <?= $_SERVER['REQUEST_URI']; ?> with Laravel
```

Run this in the [usual manner]({% post_url 2017-01-18-getting-google-app-engine-running-with-php %}) and go to http://192.168.33.30:8080/any-url. Assuming we had an actual Laravel app, we'd be done.

But now we want to offer an API. And we want to do that with Silex.
 
So create a second app.yaml, called `api.yaml`. The `service:` is the key addition, along with a different `script:`.

`> api.yaml`

```yaml
runtime: php55
api_version: 1
service: api

handlers:
- url: /.*
  script: api.php
```

And a placeholder script.

`> api.php`

```php
Using the API service to handle <?= $_SERVER['REQUEST_URI']; ?> with Silex
```

If you use the launcher script from the earlier tutorial, you'll need to change it to:

`> launch-gae.sh`

```
dev_appserver.py app.yaml api.yaml --host=0.0.0.0 --admin_host=0.0.0.0 --php_executable_path=/usr/bin/php-cgi
```

When you launch GAE, you should notice two services now get created, one on `0.0.0.0:8080` and one on `0.0.0.0:8081`. In dev, each service gets allocated it's own port number, starting at 8080. Go to http://192.168.33.30:8081/different-url and you'll have your request served by Silex.

And for dev, that's it. You can have wildcard URLs directing requests to separate codebases.

## Deployment

The deployment command changes slightly, as we have the freedom to deploy each service independently. For a full deployment, enter `gloud app deploy app.yaml api.yaml` and watch as both services get deployed in sequence.

Now, in production, you have two options of accessing the URLs.

### via HTTP

You use the service name as a part of the domain, so `http://api.{your-app-name}.appspot.com`.

### via HTTPS

Google don't issue nested wildcard SSL certificates, so the compromise is `https://api-dot-{your-app-name}.appspot.com`.

## Multi-lingual services

One thing I haven't covered (mainly because I don't have the knowledge to try it) is changing the language for a service.

According to the docs, the runtimes don't have to be the same, so you could have php55 serving your main application, but python27 handling API requests. If anyone gets this working, I'd love to hear about it.

## Summary

And that's it, you can now break your application up into as many services as you want, even go full-blown microservice! I believe you can also scale services separately, but I've not gotten that far yet.

## Tutorial Complete!

You have unlocked: how to use Services to break up your application.
