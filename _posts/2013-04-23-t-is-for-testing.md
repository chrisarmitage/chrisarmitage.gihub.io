---
layout: post
title: "T is for Testing"
comments: false
tags:
  - A to Z 2013
---

Since I became a full-time developer a few months ago, I’ve experienced a couple of “non-forecastable critical infrastructure failures on deploy”.

That is, we thought code was ready, deployed it, and it broke. Aside from lack of caffeine, this is the greatest peril a developer faces on a daily basis. It’s all very well running your script 50 times and it returning data 50 times, but what if on the first day of being live the code returns a simple error? Does it fail gracefully? Does it politely inform the user the action couldn’t be completed and to retry? Does it make the server lock up?

I’ve known of PHPUnit, a popular testing framework for PHP, for a few years, but it’s only been the last couple of months I’ve started to use it properly. The first problem is you have to write testable code. If you have something like this:

```
Get user input
Retrieve some data from the database
Make a call to the web-service
Get the result data
Update the database
Format it
Show it to the user
```

then every time you run the script it’s hitting the web-service and changing data in your database. If you happen to be charged per-call to a web-service, you’re in trouble.

The first step on my path to enlightenment was [The Grumpy Programmer’s Guide To Building Testable PHP Applications](https://leanpub.com/grumpy-testing). Whilst it only skims the use of PHPUnit, it tells you how to get your code in to a state where testing is easy. The fancy term for it is Dependency Injection. It just means your code shouldn’t depend on anything that can’t be passed in at the start of the code. DI could have a whole series of blog posts written about it (already being considered!), so lets drop to a simpler level.

For instance, let’s focus on the formatting bit of our above script. It doesn’t need to know where the data came from, or what happens to it next. So I could break it up to be

```
Get some data
Format it
Pass the data to something else
```

The outcome would be the same, but now I’ve isolated this chunk. Instead of always using the response from the web-service, I can use PHPUnit to say ‘The data is “[chris,armitage]”, the outcome should be “Chris Armitage” with no errors’. I can run this over and over without ever racking up web-service charges or messing up the database. But where it really comes in to it’s own is errors. Say the web-service itself goes down. The data it returned would be “[]”. Can my script handle that? Waiting to run it against the service that may break once every six months is impractical. But I can PHPUnit to do another run at it, this time passing in the empty data. In that circumstance it should throw an error stating the web-service is down. Does it? Yes? Great, I have confidence that my code will still work fine, even in the most bizarre conditions.

The other great power of PHPUnit is, once the tests have been written, they sit there and can be run whenever you like. Say I do something very odd for another part of the project, like turning all square brackets ([) to curly braces ({). I’ll run my tests and suddenly the formatting code that I wrote months earlier will fail, it can’t understand it’s input any more. I was working on a completely different part of the program so I wouldn’t have thought of going back and checking that bit worked. PHPUnit and testing just saved me from deploying broken code.

I’m now using PHPUnit on a daily basis, following the path of the Grumpy Programmer. 95% of the new code I’m writing is covered by tests, which gives me a huge amount of confidence that it will work first time without any 3am errors . And I like having lots of green ticks on my code…
