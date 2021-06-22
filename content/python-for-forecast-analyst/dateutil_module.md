---
title: "Python dateutil module"
date: 2021-06-20T19:57:25-06:00
draft: false
tags: ['Python', 'dateutil']
description: Date functions in Python - Part 2
---

When we need to do any operations between two date/datetime objects, for example, addition or subtraction, we will need datedelta/timedelta. Unfortunately, the timedelta class included in datetime module has limited functions (in case you are interested: [documentation](https://docs.python.org/3/library/datetime.html#timedelta-objects)). Python Numpy and Pandas have datedelta/timedelta objects, but I found that dateutil is more intuitive. Although dateutil has many powerful functions, I will only introduce one function that I use the most frequently.
<p>&nbsp;</p>

**relativedelta()**

relativedelta() is a class method of relativedelta under dateutil module. There are 2 ways to create the timedelta.

The first way:
```
relativedelta(datetime1, datetime2)
```
The second way:
```
relativedelta(arg1=x,arg2=y,arg3=z...)
```

I use the second way more often then the first way. Here is the [huge list of arguments](https://dateutil.readthedocs.io/en/stable/relativedelta.html) that we can provide to relativedelta(). I use "years", "months", "weeks", and "days" more frequently than the others. Notice, **they are all plural**, and their singular form has different meanings to this function.

It might work better if I use an example to explain how it works:
```
import datetime 
from dateutil import relativedelta # import relativedelta

dt = datetime.date(2021, 6, 1) # create date object: 2021-06-01
delta = relativedelta.relativedelta(months=+1) # create datedelta: +1
print(dt + delta) # add the delta to the date
```
The output is:
```
2021-07-01
```
The original date is 2021-06-01, and we are adding 1 month to it so the output is 2021-07-01. If we want to subtract 1 month from it, we set the argument **months to -1** (months=-1). The python code would be:
```
import datetime
from dateutil import relativedelta # import relativedelta

dt = datetime.date(2021, 6, 1) # create date object: 2021-06-01
delta = relativedelta.relativedelta(months=-1) # create datedelta: -1
print(dt + delta) # add the delta to the date
```
And the output would be:
```
2021-05-01
```
We can simply make any combination of arguments of "years", "weeks", and "days"... to create the datetimedelta that we need.

I use datedelta/timedelta pretty much in all my Python scripts if there is a need of relative dates. One example is:
```
import datetime
from dateutil import relativedelta

today = datetime.date.today() # get today's date

# get current month, use the first day so day argument is set to 1
current_month = datetime.date(today.year, today.month, 1) 

delta = relativedelta.relativedelta(months=-6) # create delta, -6 months
six_month_ago = current_month + delta # get the date of 6 month ago relative to current month
```
In this way, every time I run this script, I can always get the date of six month ago relative to current month, without the need to do any calculation or type in the date manually.