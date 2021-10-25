---
title: "Pandas Datetime Resampling"
date: 2021-10-24T00:50:45-06:00
draft: False
tags: ['Python', 'pandas', 'datetime']
description: Basics of Datetime Resampling in Pandas
---

Resampling in Pandas is a very convenient method to "aggregate" your data based on specific time interval. It works like groupby in Pandas (or GROUP BY in SQL), but it offers more flexibility. For example, the groupby method in Pandas only aggregate data based on the attributes included in a column. But resampling for datetime can "aggregate" data based on any user defined time interval. It might be easier to demonstrate with an example: given a Pandas DataFrame (df) of:
```
2021-01-01 00:00:00    0
2021-01-01 00:05:00    1
2021-01-01 00:10:00    2
2021-01-01 00:15:00    3
2021-01-01 00:20:00    4
2021-01-01 00:25:00    5
2021-01-01 00:30:00    6
2021-01-01 00:35:00    7
2021-01-01 00:40:00    8
2021-01-01 00:45:00    9
Freq: 5T, Name: value, dtype: int64
```
As shown, the frequency (time interval) is 5 minutes ("5T", refer to [Offset aliases](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#offset-aliases)).

If we want the sum of the value for every 100 seconds, we can resample it as:
```
df.resample('100S').sum()
```
which will return:
```
2021-01-01 00:00:00    0
2021-01-01 00:01:40    0
2021-01-01 00:03:20    0
2021-01-01 00:05:00    1
2021-01-01 00:06:40    0
2021-01-01 00:08:20    0
2021-01-01 00:10:00    2
2021-01-01 00:11:40    0
2021-01-01 00:13:20    0
2021-01-01 00:15:00    3
2021-01-01 00:16:40    0
2021-01-01 00:18:20    0
2021-01-01 00:20:00    4
2021-01-01 00:21:40    0
2021-01-01 00:23:20    0
2021-01-01 00:25:00    5
2021-01-01 00:26:40    0
2021-01-01 00:28:20    0
2021-01-01 00:30:00    6
2021-01-01 00:31:40    0
2021-01-01 00:33:20    0
2021-01-01 00:35:00    7
2021-01-01 00:36:40    0
2021-01-01 00:38:20    0
2021-01-01 00:40:00    8
2021-01-01 00:41:40    0
2021-01-01 00:43:20    0
2021-01-01 00:45:00    9
Freq: 100S, Name: value, dtype: int64
```
Notices that some of the Timestamps in the result dataset do not exist in the original dataset. This is not exactly aggregation, but on the contrary the grain of the data become smaller (from 5 minutes to 100 seconds). This is why I had the double quotation marks for the "aggregation" before. 

More often, I use resample() to aggregate data, for example:
```
ts.resample('20T').sum()
```
will return sum of value for every 20 minutes:
```
2021-01-01 00:00:00     6
2021-01-01 00:20:00    22
2021-01-01 00:40:00    17
Freq: 20T, Name: value, dtype: int64
```

One thing to remember: to use resample() with Pandas dataframe/Series, the index needs to be datetime Index. 
