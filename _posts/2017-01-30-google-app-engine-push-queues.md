---
layout: post
title: "Google App Engine: Push Queues"
comments: true
excerpt: "Push Queues are an effective way of making your app lightning-fast by responding to the user without waiting for a lengthy task to complete."
category: php
tags:
  - Google App Engine
---

Herein you'll find instructions on setting up Google App Engine's Push Queues, a way of making your application respond quickly and consistently instead of waiting for a slow big of logic to complete.

## Background

With modern web applications, it's all about the Time to First Byte; how quickly the user gets a response from your app. Google guidelines say [anything over 200ms is slow](https://developers.google.com/speed/docs/insights/Server). At an extreme, a standard web request on GAE [will timeout after 60 seconds](https://cloud.google.com/appengine/docs/php/how-requests-are-handled#PHP_The_request_timer), this cannot be increased.
It's understandable, nobody likes waiting to see if a web request actually went through. Certainly not on a poor mobile connection.

The solution? Acknowledge the user request and pass the request data onto another part of the code to actually process.

## Requirements

* [Have a GAE environment up and running]({% post_url 2017-01-18-getting-google-app-engine-running-with-php %})

## But... why?

So you have an app where a user signs up. The account gets created and an email gets sent out. Pretty common use case.

But there's a problem with your email gateway. Maybe it's just slow. Or it's down for maintenance. Or it's so swamped it takes 5 seconds to return a response. 5 seconds of white screen is a long time for a new user to look at, unsure if the site just crashed.

So we can break it up. For simplicity, we'll leave the user registration alone, and focus on the email. What we should do is accept the request, add the user to the database, then create a Task to send out an email. As soon as the Task is sent (which is extremely quick), we response to the user saying "Account created, email will be with you shortly".

## Stage 1: The all-in-one way.

First, we need an application config. 

`> app.yaml`
```yaml
runtime: php55
api_version: 1

handlers:
  - url: /
    script: index.php
    login: optional
```

This will route any requests to the index.php file.

And we need an application to simulate a sign-up.

`> index.php`
```php
<?php

function logDebug($message) {
    syslog(LOG_DEBUG, $message);
    error_log($message);
}

function email($emailAddress) {
    logDebug("Sending registration email to {$emailAddress}");
    sleep(5);
}

$start = microtime(true);

if (isset($_POST['email_address'])) {
    // Send the confirmation email
    email($_POST['email_address']);

    logDebug('User signup in ' . round((microtime(true) - $start) * 1000) . 'ms');
    echo 'Thank you for signing up, your email <strong>has been</strong> sent to you<hr />';
}
?>

<form method="post" action="/">
    <label for="email_address">Email address</label>
    <input type="email" name="email_address" value="email@example.com" />
    <button type="submit">Sign-up</button>
</form>

<?php
logDebug('Page responded in ' . round((microtime(true) - $start) * 1000) . 'ms');
```

We show a form, and if it's been POSTed, we pretend to send an email.

Fire up the dev server (`./launch-gae` if using the box from the earlier post) and load it up in the browser. Sign up, and it should function as expected. It's just slow.

So, lets get some queues involved!

## Stage 2: Using Queues

There are two types of Queue, Push and Pull. Push is the only one currently implemented on the PHP runtime. Your application has one Push Queue by default, called... `default`. You can view it via the [GAE Admin Panel](http://localhost:8000/taskqueue).

The Queue works by accepting a URL and some data, and calling said URL with said data. Not much more complicated than that.

First up, lets split out our user signup and mail code.

`> index.php`
```php
<?php

use google\appengine\api\taskqueue\PushTask;
use google\appengine\api\taskqueue\PushQueue;

function logDebug($message) {
    syslog(LOG_DEBUG, $message);
    error_log($message);
}

$start = microtime(true);

if (isset($_POST['email_address'])) {
    // Send the email to the user
    
    logDebug('User signup requested in ' . round((microtime(true) - $start) * 1000) . 'ms');
    echo 'Thank you for signing up, your email <strong>will be</strong> sent to you<hr />';
}
?>

<form method="post" action="/">
    <label for="email_address">Email address</label>
    <input type="email" name="email_address" value="email@example.com" />
    <button type="submit">Sign-up</button>
</form>

<?php
logDebug('Page responded in ' . round((microtime(true) - $start) * 1000) . 'ms');
```

`> email.php`
```php
<?php

function logDebug($message) {
    syslog(LOG_DEBUG, $message);
    error_log($message);
}

function email($emailAddress) {
    logDebug("Sending registration email to {$emailAddress}");
    sleep(5);
}

$start = microtime(true);

email($_POST['emailAddress']);
logDebug('User signup in ' . round((microtime(true) - $start) * 1000) . 'ms');
```

And we need a new URL mapping

`> app.yaml`
```yaml
runtime: php55
api_version: 1

handlers:
  - url: /
    script: index.php
    login: optional

  - url: /task/email
    script: email.php
    login: admin
```

The groundwork is done, we just need to create the Task and send it to the Push Queue

`> index.php`
```php
<?php

use google\appengine\api\taskqueue\PushTask;
use google\appengine\api\taskqueue\PushQueue;

function logDebug($message) {
    syslog(LOG_DEBUG, $message);
    error_log($message);
}

$start = microtime(true);

if (isset($_POST['email_address'])) {
    $queue = new PushQueue();
    $tasks = [
        new PushTask(
            '/task/email',
            [
                'emailAddress' => $_POST['email_address'],
            ]
        )
    ];
    $queue->addTasks($tasks);
    logDebug('User signup requested in ' . round((microtime(true) - $start) * 1000) . 'ms');
    echo 'Thank you for signing up, your email <strong>will be</strong> sent to you<hr />';
}
?>

<form method="post" action="/">
    <label for="email_address">Email address</label>
    <input type="email" name="email_address" value="email@example.com" />
    <button type="submit">Sign-up</button>
</form>

<?php
logDebug('Page responded in ' . round((microtime(true) - $start) * 1000) . 'ms');
```

Now, when you submit the form, the page will return almost immediately, and the mail will get sent by another process in the background. If you look at the access logs, you'll see something like:

```
08:33:10.360   POST   200   323 B   276 ms   Chrome 55   /
08:33:10.687   POST   200    25 B     5 s    AppEngine   /task/email
```

So the user form returned in 276ms, and a separate request to `/task/mail` from the AppEngine itself took 5 seconds to complete.

Queues are extremely powerful for web apps, and there's a lot more to cover, including security and performance settings. But that's for another time.

## Tutorial Complete!

You have unlocked: how to use Push Queues to break up your application and make it more responsive to the user.
