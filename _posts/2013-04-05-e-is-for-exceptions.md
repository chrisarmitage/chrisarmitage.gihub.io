---
layout: post
title: "E is for Exceptions"
comments: false
category: php
tags:
  - A to Z 2013
---

Another PHP-specific post here, but one I’m hoping to get feedback on from other devs.

During our refactoring, I’m pushing for much better error reporting. At the forefront of this is single-use exceptions, each exceptions should only be triggered by one cause. This can lead to a whole lotta exception classes, but I reckon it’s worth it.

My current set up is a NameOrAddressMissingException, which is triggered if a SOAP call comes back with the corresponding error code set.

This extends from the PlaceOrderServiceException, from which any other SOAP parsing error extends off. This, in turn, extends the ServiceSupplierException. And so on, until I end up with something like this.

```
Exception
  CoreException
    WebServiceException
      ServiceSupplierException
        PlaceOrderServiceException
          NameOrAddressMissingException
          InvalidItemException
          NoAvailabilityException
```

In CoreException I have some basic logging code. This will be upgraded to Monolog and Graphite. The theory is a NameOrAddressMissingException will trigger five exceptions to be logged. So instead of having a few hundred graphs on Graphite to monitor, I can load up the WebServiceExceptionCount and get an overview if any webservices are in trouble.

What I need to work out now is the best way to catch them.

I’m tempted to go down the route of

```php?start_inline=1
try {
  $order = new Order();
} catch (NoAvailabilityException $e) {
  echo "Sorry, not available"
}
```

But I feel this violates a dozen rules on OOP. A high level piece of code is reliant on an exception a few levels deeper. Unfortunately I’ve scoured the internets and not come up with anything that would point me in a good direction.

So, my fellow developers, do you have any experience or input on this? I’m happy to go more in-depth, but as this is part of my A-to-Z challenge I don’t want to scare away too many other participants.
