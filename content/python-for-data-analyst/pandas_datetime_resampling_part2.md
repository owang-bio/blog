---
title: "Pandas Datetime Resampling - Part 2"
date: 2021-10-25T22:47:39-06:00
draft: False
tags: ['Python', 'pandas', 'datetime']
description: Closed and Label Arguments of Resampling in Pandas
---

Pandas's documentation about the **closed** and **label** argument for resample() is somewhat confusing, which is why I decided to write this post. 

### **1. Closed**
Let's start with following dataset (variable name: ts) which has a frequency of day, and a value of 1 for each date:
```
2021-01-01    1
2021-01-02    1
2021-01-03    1
2021-01-04    1
2021-01-05    1
2021-01-06    1
2021-01-07    1
2021-01-08    1
2021-01-09    1
2021-01-10    1
2021-01-11    1
2021-01-12    1
Freq: D, Name: value, dtype: int64
```

Now if we resample it (and get the sum of values) to a frequency of 5-day, and add a keyword argument **closed**:
```
ts.resample('5D', closed='left').sum()
```
we will get:
```
2021-01-01    5
2021-01-06    5
2021-01-11    2
Freq: 5D, Name: value, dtype: int64
```
With **closed='left'**, we are basically setting the 5-day bins as following:
```
2021-01-01 <= date < 2021-01-06 (2021-01-01 + 5 days)
2021-01-06 <= date < 2021-01-11 (2021-01-06 + 5 days)
2021-01-11 <= date < 2021-01-16 (2021-01-11 + 5 days)
```
Notice that the "<="sign is on the **left**, and that's exactly what the **closed='left'** does!

Now let's try  **closed='right'**:
```
ts.resample('5D', closed='right').sum()
```
and things start getting a little confusing, because we get:
```
2020-12-27    1
2021-01-01    5
2021-01-06    5
2021-01-11    1
Freq: 5D, Name: value, dtype: int64
```
"2020-12-27" is not even in the original dataset, what is causing it showing up in the resampling results?

The answer is simple, same as the above example, **closed='right'** just tells Pandas to put the "<=" on the right side as this:
```
2021-01-01 < date <= 2021-01-06 (2021-01-01 + 5 days)
2021-01-06 < date <= 2021-01-11 (2021-01-06 + 5 days)
2021-01-11 < date <= 2021-01-16 (2021-01-11 + 5 days)
```
but what about "2021-01-01" which is not included in the first bin, but we did have a data point on that date. So, Pandas went one bin backwards:
```
2020-12-27 (2021-01-01 - 5 days) < date <= 2021-01-01
```
Now, the results included everything from the original dataset which are aggregated in a 5-day interval. 

### **1. Label**
The above example also helps with understanding the **label** argument. Let's revisit the **first example above** to demonstrate how to use it.

**label='right'** uses the dates on the right as the label of resampling results. For example:
```
ts.resample('5D', closed='left', label='right').sum()
```
returns:
```
2021-01-06    5
2021-01-11    5
2021-01-16    2
Freq: 5D, Name: value, dtype: int64
```
while **label='left'** uses the dates on the left as the label
```
ts.resample('5D', closed='left', label='left').sum()
```
returns:
```
2021-01-01    5
2021-01-06    5
2021-01-11    2
Freq: 5D, Name: value, dtype: int64
```

The **label** argument has different default value, it can be either 'left' or 'right' based on the datetime frequency we are resampling to. I would recommend always specifically telling Pandas how you want to label the result DataFrame/Series.
