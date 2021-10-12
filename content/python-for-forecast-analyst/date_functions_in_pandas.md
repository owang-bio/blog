---
title: "Timestamp in Pandas"
date: 2021-10-09T14:47:39-06:00
draft: False
tags: ['python', 'datetime', 'pandas']
description: Introduction to Pandas's TimeStamp
---

### **Instantiation**
Timestamp in Pandas is similar to Python’s Datetime objects, but Pandas's Timestamp is more flexible when it comes to instantiation. 
For example, the following code does the same thing to return a Timestamp('2018-01-05 00:00:00'):
```
pd.Timestamp("2018-01-05")
pd.Timestamp("01-05-2018")
pd.Timestamp("01 05 2018")
pd.Timestamp("2018/01/05")
```
But I would stick with one format, for example, "2018-01-05".

It is also convenient to get the Timestamp of right now:
```
pd.Timestamp("today")
```

Using key word arguments will also work, for example:
```
pd.Timestamp(year=2020, month=9, day=1)
```

<p>&nbsp;</p>

### **Functions I Use Frequently**
I use the following three methods with Timestamp the most frequently.
1. **date()** will convert a Timestamp to date, for example:
```
pd.Timestamp('2021-09-01').date()
```
2. **normalize()** will zero time part, for example, if we have a Timestamp('2021-10-12 06:33:30.793655'), once normalized() is called it will become Timestamp('2021-10-12 00:00:00'):
```
pd.Timestamp('2021-10-12 06:33:30.793655').normalize()
```
3. **replace()** will replace the date/time part, for example:
```
pd.Timestamp("2018/01/05").replace(month=9)
```
replace the month from January to September and return Timestamp('2018-09-05 00:00:00')
```
pd.Timestamp('2021-10-12 06:33:30.793655').replace(second=0)
```
will replace second from 30 to 0 and return Timestamp('2021-10-12 06:33:00.793655').

<p>&nbsp;</p>

### **Some Useful Attributes**
Same as datetime objects, dot notation will get the attributes of Timestamps for you.
I use the following attributes the most:
```
pd.Timestamp("today").year
pd.Timestamp("today").month
pd.Timestamp("today").day
pd.Timestamp("today").dayofweek
pd.Timestamp("today").dayofyear
pd.Timestamp("today").quarter
...
```
The full list of attributes and methods can be found [here](https://pandas.pydata.org/docs/reference/api/pandas.Timestamp.html)