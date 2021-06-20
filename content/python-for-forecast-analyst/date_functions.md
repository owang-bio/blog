---
title: "Date Functions - Part 1"
date: 2021-06-15T23:58:51-06:00
draft: false
tags: ['python', 'datetime']
description: Some useful date functions in Python
---

As a forecast analyst, I am dealing with time series data every day. So, the first Python module I want to mention is Python datetime which provides basic classes for dates and time manipulation. 

For my current job, although I am helping several international markets, most of the time I don't need to worry about time zone or dailylight saving time, etc. If you are interested in the datetime type that include such information, you can learn more about it from the [documentation](https://docs.python.org/3/library/datetime.html#date-objects).

In this post, I will introduce several classes in this module and class methods that I use the most frequently. Then, my next post will explain the use case for them. 

Of course, to use this module, we first need to import it:
```
import datetime
```
<p>&nbsp;</p>

One useful class in datetime module is datetime(). When instantiating, the arguments, year, month and day are required, and the others are optional:
```
datetime(year, month, day[, hour[, minute[, second[, microsecond[,tzinfo]]]]])
```
<p>&nbsp;</p>

It instantiates a datetime object which has these attributes: year, month, day, hour, minute, second, microsecond, and tzinfo.
For example:
```
dt = datetime.datetime(2021, 6, 1)
```
will return the following object:
```
datetime.datetime(2021, 6, 1, 0, 0)
```
<p>&nbsp;</p>

The following code is how we can access it's year, month and day attributes, and you can access the other attributes using the dot notation. They are all integers.
For example:
```
dt.year
dt.month
dt.day
```
will return:
```
2021
6
1
```
<p>&nbsp;</p>

Another class that I use frequently is date(). It is very similar to datetime() class, but when we instantiate it, it only takes three arguments: year, month, day. They way to access it's attribute is the same as that of datetime objects. 
For example:
```
dt = datetime.date(2021, 6, 1)
dt.year
```

date() class has a today() method, it does not take any arguments, but return the today's date in local time.
```
datetime.date.today()
```
<p>&nbsp;</p>

Sometime we need to convert date/datetime objects into string format or _vice versa_, which is when strftime() and strptime() come in handy.

datetime and date objects have a strftime() method that converts them into string, in a format that we can specify. 
For example:
```
dt = datetime.date(2021, 6, 1) #create a date object
dt.strftime('%Y-%m-%d') #convert the date to string
```
will convert the date to "2021-06-01" in string type. "%Y", "%m", and "%d" are called format Codes. [Here](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior) are more information about different format code that you can use to format your date in string. If we want the date string in the format of "Jun-01-21", we can use format code of "%B-%d-%y":
```
dt.strftime(format="%B-%d-%y")
```
