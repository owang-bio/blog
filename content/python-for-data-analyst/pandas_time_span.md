---
title: "Pandas Time Span"
date: 2021-10-22T00:50:03-06:00
draft: False
tags: ['Python', 'pandas', 'datetime', 'period']
description: Time span objects in Pandas
---

Pandas time span represents a period of time, for example, one day, one month, or one year... It is also called period object. I do not use time span object very often, but it is one of four pandas objects related to datetime. So, I am writing a very brief introduction for time span. 

To create a time span object, we can use Period() method:
```
pd.Period()
```
The keyword argument **freq** specify the span of the time. A list of valid input for **freq** argument can be find [here](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#offset-aliases), which are called Offset aliases. Not sure why Pandas documentation for time span did not mention that **freq** takes offset aliases. I think it might be helpful to go through the list of the Offset aliases if you are going to deal with Pandas time span. 

Pandas can infer the time span if freq argument is not provided. For example:
```
pd.Period('2021')
```
will return a Period('2021', 'A-DEC') object with frequency of 1 year ('A-DEC' just indicates that it is year time span, and the month is December). Time span has some unique attributes, compared with Pandas Timestamp. For example 'start_time' and 'end_time' will return the start and end of the time span, respectively. 

Timestamp objects have to_period() method to convert Timestampe into time span, and time span's method to_period() will convert a time span into Timestamp. The asfreq() method can change the frequency of a time span object.

A use case of time span is to aggregate the data into different frequency, when the index is not datetime. For example, from daily values into monthly or annually values. That can also be done with Pandas resample() method, and I will have a separated post about it.



