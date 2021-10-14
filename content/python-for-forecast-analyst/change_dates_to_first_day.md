---
title: "Date to beginning of Month"
date: 2021-10-13T22:45:50-06:00
draft: False
tags: ['python', 'pandas', 'datetime']
description: change a Pandas Series of dates to the first day of each month
---

Pandas.Timestamp.replace(day=1) can change a single Timestamp to the beginning of a month. But when we have a Series of date or a date column, this method will no longer work. I have to do 
do this every once in a while. I have used several methods, and I am summarizing them in this post. 

First let me create a small Pandas's DataFrame containing a column of dates:

|ID | Dates              |
|:-|:--------------------|
|1 | 2021-01-23 14:05:21 |
|2 | 2021-02-05 05:07:30 |
|3 | 2021-02-18 17:08:51 |
|4 | 2021-01-01 05:07:30 |
|5 | 2021-01-31 05:07:30 |

### **Method 1. pd.tseries.offsets.MonthBegin()**

pd.tseries.offsets.MonthBegin() seems like an intuitive method:
```
date_df['Dates'] + pd.tseries.offsets.MonthBegin()
```
However, it returns the beginning of next month:
| Dates               |
|:--------------------|
| 2021-02-01 14:05:21 |
| 2021-03-01 05:07:30 |
| 2021-03-01 17:08:51 |
| 2021-02-01 05:07:30 |
| 2021-02-01 05:07:30 |
Therefore, we need to offset -1 month from the results:
```
date_df['Dates'] + pd.tseries.offsets.MonthBegin() + pd.DateOffset(months=-1)
```
and now we have correct results:
| Dates               |
|:--------------------|
| 2021-01-01 14:05:21 |
| 2021-02-01 05:07:30 |
| 2021-02-01 17:08:51 |
| 2021-01-01 05:07:30 |
| 2021-01-01 05:07:30 |

### **Method 2. Change Type to "datetime64[M]"**

If hour, minutes and seconds are not important, changing the datetime type to "datetime64[M]" will also work:
```
date_df['Dates'].astype('datetime64[M]')
```
will return:
| Dates      |
|:-----------|
| 2021-01-01 |
| 2021-02-01 |
| 2021-02-01 |
| 2021-01-01 |
| 2021-01-01 |

### **Method 3. Change to Pandas's datetime Span and change it back to Timestamp**
Pandas's datetime span deserve a separated post, and if you haven't read it yet [click here](#).

The following code changes Timestamps to Pandas's datetime span and then change it back to Timestamp
```
date_df['Dates'].dt.to_period('M').dt.to_timestamp('ms')
```
Specifically, to_period('M') changes the dates to month span, and to_timestamp('ms') converts the spans back to Timestamps. Now we have:
| Dates      |
|:-----------|
| 2021-01-01 |
| 2021-02-01 |
| 2021-02-01 |
| 2021-01-01 |
| 2021-01-01 |

dt is just accessor for Timestamp like objects. You need dt accessor for a method (e.g. to_period('M')) to access the Timestamps inside the date_df['Dates'] Series.
