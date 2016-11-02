---
layout: post
title: "S is for Stats"
comments: false
tags:
  - A to Z 2013
---

Following on from the Graphite post, I’ve started to get my head around how to use it properly in the business. It helps that some of the metrics I’m tapping in to are now producing a lot of data, so I can prod and poke them to see what they can tell me. It’s all very well having a line on the page that tells me how many searches we’ve done, but it needs to be more… understandable.

First up, I found `summarize()`. With data being added to the graph every 10 seconds, the lines can get very busy. Searches can fluctuate quickly, so it becomes a mess to read. The summarize() command can total up all the searches into chunks, say one hour blocks. It’s now clear to see we did 5000 searches in one hour, followed by 5800 in the next. With it now plotting 24 data points (we generally look at things on a day basis) the graph is much easier to look at and understand quickly.

Once I had data that was clearer, I wanted to do some comparisons. Whilst devs can look at script execution graphs all day, management like to see how the business is performing. Using the `timeShift()` command, I can adjust single lines on the graph forwards or backwards in time. I can have the last 24 hours of data overlaid by the data from 24 to 48 hours ago. We’re now at 48 data points (still very clear on the graph) but you can start to see patterns emerging. Saturday evenings are quieter than Sunday evenings. Makes perfect sense. But a sudden drop in searches on a Wednesday afternoon compared to the Tuesday at the same time? Best go checking the logs, see if something happened.

However, to really make it easy to see what’s going on, I found the `diffSeries()` command. It’s designed to take two graph lines and subtract one from the other. Combining this with the timeShift() technique, I have a single line telling me the difference, by the hour, of the number of searches. 16:00 and we were fairly even, 18:00 and we were doing 1000 searches an hour more than the same time period on the previous day. Now we have the kind of stats that management can make use of, using the exact same system that I deployed to keep track of websevice call lengths (the kind of thing management go glazed-eyes at).

And with management happy, I get cake.

(I don’t actually get cake, but I might start charging it for making new graphs for them)
