---
title: "Python datetime module"
date: 2021-06-15T23:58:51-06:00
draft: false
tags: ['python', 'datetime']
description: Date functions in Python - Part 1
---

As a forecast analyst, I am dealing with time series data every day. So, the first Python module I want to mention is datetime which provides basic classes for dates and time manipulation. 

I am helping several international markets with forecasting, most of the time I don't need to worry about time zone or daylight saving time, etc. If you are interested in the datetime type that include such information, you can learn more about it from the [documentation](https://docs.python.org/3/library/datetime.html#date-objects).

In this post, I will introduce several basic classes and class methods in datetime module that I use the most frequently. Later, we will revisit these classes and methods in my future posts explaining how I use them in my work. 
<p>&nbsp;</p>

Of course, to use this module, we first need to import it:
```
import datetime
```
<p>&nbsp;</p>

### **datetime()**

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

### **date()**
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

### **strftime()** and **strptime()**
Sometime we need to convert date/datetime objects into string format or _vice versa_, which is when strftime() and strptime() come in handy. date/datetime objects have a strftime() method that converts them into strings, in a format that we can specify. 
For example:
```
dt = datetime.date(2021, 6, 1) #create a date object
dt.strftime('%Y-%m-%d') #convert the date to string
```
will convert the date to "2021-06-01" in string type. "%Y", "%m", and "%d" are format codes. For example, with "%Y" you are telling the strftime() to convert the year into format of "2021". You can read more details about the format codes [here](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior). If we want the date string in the format of "June-01-21", we can use format code of "%B-%d-%y":
```
dt.strftime(format="%B-%d-%y")
```

strptime() is a class method of datetime module's datetime class. So, to use this method, you need to call:
```
datetime.datetime.strptime(datetime_in_str, '%Y-%m-%d')
```
for example:
```
datetime.datetime.strptime('2021-06-20', '%Y-%m-%d')
```
will return the following datetime object:
```
datetime.datetime(2021, 6, 20, 0, 0)
```
One thing to remember is that strptime() does not take keyword arguments, which means we need to provide the date in string format as the first argument, and the format code as the second argument, and we don't give any names to the arguments like we did for strftime (e.g. dt.strftime(**format**="%B-%d-%y")).

That's it for the datetime module from me. Hope you it is helpful to some of you!