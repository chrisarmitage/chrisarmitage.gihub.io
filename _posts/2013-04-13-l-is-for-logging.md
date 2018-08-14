---
layout: post
title: "L is for Logging"
comments: false
tags:
  - A to Z 2013
published: false
---

I would hope that most devs realise the importance of good logging, even if we don’t do it.

Since the introduction of the [PSR-3 standard](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md), there’s now very little stopping you from getting a fairly decent logging system in place. I was able to get Monolog up and running in half an hour. This presented me with the next problem; what to log?

I was having issues with a webservice script that’s called via AJAX. Something a user did three hours previous could affect the script. And generally by the time someone told me there was an issue, all hope of getting anything meaningful from the user’s system had gone.

So I started off with a simple entry telling me the service had been called, so I could track execution times. Nothing fancy, call microtime() at the start and end, do a little maths.

```
2013-04-13 09:00:00 - INFO    : Request to web service
2013-04-13 09:00:04 - INFO    : Request completed 3.84 seconds
```

Already I can start to identify if a script is running slowly. Also inadvertently, if the second line is missing I know the script never completed.

Seemingly random failures did pop up, so within my main SOAP call I added a couple more lines.

```
2013-04-13 09:00:00 - INFO    : Request to web service
2013-04-13 09:00:00 - DEBUG   : > Webservice\Call
2013-04-13 09:00:03 - DEBUG   : < Webservice\Call - result = SUCCESS
2013-04-13 09:00:04 - INFO    : Request completed 3.84 seconds
```

Basic, but now I can go back and see if a webservice really did fail or if it was a user error. A few more iterations and I now had something like this

```
2013-04-13 09:00:00 - INFO    : Request to web service
2013-04-13 09:00:00 - DEBUG   : > Auth\ValidUser
2013-04-13 09:00:00 - DEBUG   : < Auth\ValidUser - result = SUCCESS
2013-04-13 09:00:00 - DEBUG   : > Webservice\MainFunction
2013-04-13 09:00:00 - DEBUG   : > Webservice\SubFunction
2013-04-13 09:00:00 - DEBUG   : < Webservice\Subfunction
2013-04-13 09:00:00 - DEBUG   : > Webservice\Call
2013-04-13 09:00:03 - DEBUG   : < Webservice\Call - result = SUCCESS
2013-04-13 09:00:03 - DEBUG   : > DB\LogCall
2013-04-13 09:00:04 - DEBUG   : < DB\LogCall
2013-04-13 09:00:04 - DEBUG   : < Webservice\MainFunction
2013-04-13 09:00:04 - INFO    : Request completed 3.84 seconds
```

Now I’m able to follow the flow of the call through a few different classes, and even do some very basic profiling. This method would be unsuitable for any major application, but if you’re just looking for a simple way of starting, this technique has helped me avoid a lot of frustration.

As for the technical implementation, that’s for another day (hint, Monday is M)
