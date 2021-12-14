---
title: "Time deltas and Date offsets in Pandas"
date: 2021-10-12T23:06:19-06:00
draft: False
tags: ['python', 'datetime', 'pandas']
description: Introduction to Pandas's Time deltas and Date offsets
---

Pandas's Timedelta represents the difference between two Timestamps, but there is a little difference from the dateutil.relativedelta that I introduced in another post, which is equivalent to the Dateoffsets in Pandas. I was once confused about Pandas's Timedelta and Dateoffsets, so I decided to write a post about them today. 

Timedelta and Dateoffsets are almost the same when time zone information is not provided. When time zone is provided, one difference is that Timedelta of one day adds 24 hours to a Timestamp, while DateOffset of one day adds a day to a Timestamp. There will be difference when daylight saving is taken into consideration. 

I am using an example to show you the difference.
pd.Timedelta(days=1) add 24 hours to a Timestamp:
```
pd.Timestamp("2021/11/07", tz = 'US/Central') + pd.Timedelta(days=1)
```
will return
```
Timestamp('2021-11-07 23:00:00-0600', tz='US/Central')
```
because daylight saving ends on 2021-11-07, and there are 25 hours in that day. So the date is not changed even though 24 hours were added to it.

While pd.DateOffset(days=1) add a full day to a Timestamp, no matter how many hours are there in that day:
```
pd.Timestamp("2021/11/07", tz = 'US/Central') + pd.DateOffset(days=1)
```
returns
```
Timestamp('2021-11-08 00:00:00-0600', tz='US/Central')
```
and it gives you the date of one day after at the same time.

When the difference is less than an hour, Timedelta and DateOffset are the same.



