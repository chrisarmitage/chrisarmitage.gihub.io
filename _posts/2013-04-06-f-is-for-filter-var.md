---
layout: post
title: "F is for filter_var"
comments: false
category: php
tags:
  - A to Z 2013
published: false
---

Another quick PHP one today. I’ve seen some pretty funky regex in the past when validating an email address, and even then most didn’t follow the RFC. Why bother when PHP can do all the magic for you?

```php?start_inline=1
$addresses = array(
  'chris@test.com',
  'bob',
  'som@@',
  'tht@co .com');

foreach ($addresses as $address) {
  echo "{$address} is ";
  if (filter_var($address, FILTER_VALIDATE_EMAIL)) {
    echo "valid\n";
  } else {
    echo "invalid\n";
  }
}
```
