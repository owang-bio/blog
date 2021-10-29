---
title: "Pandas Datetime index"
date: 2021-10-25T00:01:37-06:00
draft: False
tags: ['Python', 'pandas', 'datetime']
description: Datetime as index in Pandas Series/DataFrame
---

Pandas Datatime index helps the performance when we need to do a lot of calculations associated with datetime. It also provides convenient methods to access the data from a DataFrame/Series because datetime has attributes such as **month**, **year**... In addition, the resample() will work only if the index is datetime. 

Let's use a DataFrame with datetime index to demonstrate some of its use cases. The DataFrame df is defined as:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-31</th>
      <td>0</td>
    </tr>
    <tr>
      <th>2021-05-31</th>
      <td>1</td>
    </tr>
    <tr>
      <th>2021-09-30</th>
      <td>2</td>
    </tr>
    <tr>
      <th>2022-01-31</th>
      <td>3</td>
    </tr>
    <tr>
      <th>2022-05-31</th>
      <td>4</td>
    </tr>
    <tr>
      <th>2022-09-30</th>
      <td>5</td>
    </tr>
    <tr>
      <th>2023-01-31</th>
      <td>6</td>
    </tr>
    <tr>
      <th>2023-05-31</th>
      <td>7</td>
    </tr>
    <tr>
      <th>2023-09-30</th>
      <td>8</td>
    </tr>
  </tbody>
</table>

First, if we want to access data for year of 2021, we only need to call:
```
df['2021']
```
and we will get:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-31</th>
      <td>0</td>
    </tr>
    <tr>
      <th>2021-05-31</th>
      <td>1</td>
    </tr>
    <tr>
      <th>2021-09-30</th>
      <td>2</td>
    </tr>
  </tbody>
</table>

Or, if we want data form 2021 to 2022, we can do:
```
df['2021':'2022']
```
This is so much less code than using conditions to select rows from the DataFrame.

We can also access data using the attributes of datetime object. For example, we can select the rows for January of each year:
```
df[df.index.month == 1]
```
and we will get:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-31</th>
      <td>0</td>
    </tr>
    <tr>
      <th>2022-01-31</th>
      <td>3</td>
    </tr>
    <tr>
      <th>2023-01-31</th>
      <td>6</td>
    </tr>
  </tbody>
</table>

A list of datetime attributes that we can use to access datetime index can be find [here](https://pandas.pydata.org/docs/user_guide/timeseries.html#time-date-components).

To aggregate the data, we can use resample() method. For example:
```
df.resample('8M').sum()
```
will return the sum of values for a bin size of 8 months:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-31</th>
      <td>0</td>
    </tr>
    <tr>
      <th>2021-09-30</th>
      <td>3</td>
    </tr>
    <tr>
      <th>2022-05-31</th>
      <td>7</td>
    </tr>
    <tr>
      <th>2023-01-31</th>
      <td>11</td>
    </tr>
    <tr>
      <th>2023-09-30</th>
      <td>15</td>
    </tr>
  </tbody>
</table> 

More information about resample() can be find in another two posts([part 1]({{< ref "/content/python-for-data-analyst/pandas_datetime_resampling.md" >}}) and [part 2]({{< ref "/content/python-for-data-analyst/pandas_datetime_resampling_part2.md" >}})).