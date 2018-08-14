---
layout: post
title: "D is for DatePeriod"
comments: false
category: php
tags:
  - A to Z 2013
published: false
---

> Tech post: For anyone following along with the A-to-Z challenge, this is the first of what will hopefully be many tech posts. I don’t expect non-PHP programmers to have any interest here. But thanks for checking in, go grab yourself a cuppa.

We’re overhauling some of the codebase at work. We currently have a dropdown list which shows the next 18 months. This is created by a few nested loops and some witchcraft. If only there was a more civilised way preparing this…

Introducing DatePeriod

```php?start_inline=1
// Create the first entry in our list
$startDate = new \DateTime('next month', new \DateTimeZone('UTC'));

// Create the interval object (Period of 1 Month)
$interval = new \DateInterval('P1M');

// Create the DatePeriod object, with 18 occurrances (of the interval)
$months = new \DatePeriod($startDate, $interval, 18);

// We can now loop through our new DatePeriod shiny
foreach ($months as $month) {
    echo $month->format('F Y') . "\n";
}
```

And just like that, we have

```
May 2013
June 2013
July 2013
August 2013
September 2013
October 2013
November 2013
December 2013
January 2014
February 2014
March 2014
April 2014
May 2014
June 2014
July 2014
August 2014
September 2014
October 2014
November 2014
```

All that needs doing is wrapping it in some `<option>` tags and the dropdown is ready for use.
