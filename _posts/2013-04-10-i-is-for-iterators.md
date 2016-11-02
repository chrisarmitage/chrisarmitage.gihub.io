---
layout: post
title: "I is for Iterators"
comments: false
category: php
tags:
  - A to Z 2013
---

We have a script at work that’s getting a little unruly. We need a couple of nested foreach loops to do some matching and processing. But it’s getting to the point where it’s hitting the PHP memory cap. I did some snooping around, and found that using [foreach can cause memory issues](http://sldn.softlayer.com/blog/dmcaloon/PHP-Memory-Management-Foreach).

One way around this is to use the Standard PHP Library (SPL) Iterators. The iterator objects can be extremely powerful, but can also be used with only minor refactoring.

This should give you an idea of our current script:

```php?start_inline=1
// Set up three arrays with 1000 elements each
$dummyOuter = array_fill(0, 1000, null);
$dummyMiddle = array_fill(0, 1000, null);
$dummyInner = array_fill(0, 1000, null);

foreach ($dummyOuter as $do) {
  foreach ($dummyMiddle as $dm) {
    foreach ($dummyInner as $di) {
      // Do our processing code
    }
  }
}

echo "Memory usage: " . memory_get_peak_usage() . "\n";
```

That gives us 517992 bytes at peak usage. Now a little refactor to use the iterators:

```php?start_inline=1
// Set up three arrays with 1000 elements each
$dummyOuter = array_fill(0, 1000, null);
$dummyMiddle = array_fill(0, 1000, null);
$dummyInner = array_fill(0, 1000, null);

$iterOuter = new \ArrayIterator($dummyOuter);
$iterMiddle = new \ArrayIterator($dummyMiddle);
$iterInner = new \ArrayIterator($dummyInner);

foreach ($iterOuter as $do) {
  foreach ($iterMiddle as $dm) {
    foreach ($iterInner as $di) {
      // Do our processing code
    }
  }
}

echo "Memory usage: " . memory_get_peak_usage() . "\n";
```

Now we’re at 809056, so as yo… wait… that’s higher!

Now when I set out to write this post, I knew the principles but I’d never actually sat down and written any benchmark tests. Maybe I was a little too trusting of resources I’d read without double-checking the logic. Maybe the overhead of the iterator objects is causing the problem. Maybe if the array elements were objects (like they actually are in our code) instead of null elements the memory saving will overcome the overhead. Maybe I have my understanding of Iterators wrong. Whatever the cause, this requires further investigation.

Don’t take the above as a reason to ignore the SPL Iterators as just a memory hog, they are a great tool to add to your repertoire. Just double-check which is going to be the best method for you.
