---
title: "Date to End of a Month"
date: 2021-10-18T23:27:22-06:00
draft: False
tags: ['python', 'pandas', 'datetime']
description: Change a Pandas Series of dates to the last day of each month
---
Occasionally, I need to convert a series/column of dates to the last day of the same month. This post will introduce two methods that I have used. 

First let me create a small Pandas's DataFrame (saved as date_df variable) containing a column of dates:

|ID | Dates              |
|:-|:--------------------|
|1 | 2021-01-23 14:05:21 |
|2 | 2021-02-05 05:07:30 |
|3 | 2021-02-18 17:08:51 |
|4 | 2021-01-01 05:07:30 |
|5 | 2021-01-31 05:07:30 |

### **Method 1. pd.tseries.offsets.MonthEnd()**
pd.tseries.offsets.MonthEnd() works in most of cases, for example:
```
pd.Timestamp('2021-09-01') + pd.tseries.offsets.MonthEnd()
```
will return:
```
Timestamp('2021-09-30 00:00:00')
```

However, it does not work well if the date is already the last day, for example:
```
pd.Timestamp('2021-09-30') + pd.tseries.offsets.MonthEnd()
```
will return the last day of next month:
```
Timestamp('2021-10-31 00:00:00')
```
We can solve this issue with a helper function with a test using pandas Timestamp's attribute "is_month_end":
```
to_end = lambda x: x if x.is_month_end else x + pd.tseries.offsets.MonthEnd()
```
now if you call "to_end" on a Timestamp, it will return correct wanted results:
```
to_end(pd.Timestamp('2021-09-03'))
```
```
Timestamp('2021-09-30 00:00:00')
```
You can use DataFrame.apply() to apply this function to all the rows, but the performance will be bad if the it is a large dataset. I would not use it if I have more than 10,000 rows in the dataset. For larger dataset, Pandas.where() will perform better:
```
date_df['Dates'].where(
    date_df['Dates'].dt.is_month_end
    , date_df['Dates'] + pd.tseries.offsets.MonthEnd()
    )
```

### **Method 2. Change to Pandas's datetime Span and change it back to Timestamp**
```
(
    date_df['Dates'].dt.to_period('M')
        .dt.to_timestamp('M')
)
```
to_period('M') will first convert the date column to month span and then to_timestamp('M') will convert the date to the last day of each month.

One thing worth noting is that to_timestamp('ms') will convert each date to the first day of each month.  

