---
title: "Dealing With Missing Values With Pandas"
date: 2021-10-29T22:37:58-06:00
draft: False
tags: ['Python', 'pandas']
description: Pandas NA, .isna(), .notna(), .fillna(), .dropna(), .interpolate()
---

We are not living in a perfect world, so there are almost always some missing data. This post only introduce Pandas's methods that allow us to work with missing values. I will write about techniques to replace NAs in data analytics posts. 

First, Pandas has its own NA objects, which can be created by calling **pandas.NA**. However, pandas.NA is a little weird because we cannot convert a Series/DataFrame into 'float' data type if it contains pandas.NA, while numpy.nan works just fine. So, I would recommend sticking with numpy.nan if we want to input NA values into a Pandas Series/DataFrame.

### **1. .isna()/.notna()**

.isna() is a pandas function, and DataFrame and Series also have .isna() methods. We can apply pandas.isna() on DataFrame/Series, or directly call the .isna() method of a DataFrame/Series. For example:
```
pandas.isna(df)
df.isna()
```
will return the same results. The .isna() method return True if the element is NA, and False if not. **.notna()** just does exact the opposite things as .isna().

To demonstrate **.dropna()** and **.fillna()**, let's first set up a DataFrame df:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>value_1</th>
      <th>value_2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>&lt;NA&gt;</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>&lt;NA&gt;</td>
      <td>&lt;NA&gt;</td>
    </tr>
  </tbody>
</table>

### **2. .dropna()**
.dropna() by default will remove any rows with at least one NA values. But we can set the threshold. For example:
```
df.dropna(thresh=2)
```
will keep rows with number of NAs less than the threshold:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>value_1</th>
      <th>value_2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>&lt;NA&gt;</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>1</td>
    </tr>
  </tbody>
</table>

Also, a **axis** argument will specify whether we are removing rows or columns. For example:

```
df.dropna(axis=1, thresh=2)
```
will remove columns with more than 1 NAs and return:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>value_1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <td>3</td>
      <td>&lt;NA&gt;</td>
    </tr>
  </tbody>
</table>

### **3. .fillna()**
.fillna() replace NA with provided value or existing values. For example:
```
df.fillna(-1)
```
will replace all the NAs to -1:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>value_1</th>
      <th>value_2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>-1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>-1</td>
      <td>-1</td>
    </tr>
  </tbody>
</table>

.fillna() has a helpful argument, **method**, which takes ['backfill', 'bfill', 'pad', 'ffill', None]. "pad"/"ffill" uses the last non-NA value to replace following NAs, while "backfill" / "bfill" uses the next non-NA value to replace preceding NAs. The **axis** argument will specify whether we are replacing NAs by columns or rows. 

Pandas also have an more sophisticated method to replace NAs, **.interpolate()**. It works even we don't provide any arguments, but I would highly recommend to read its [documentations](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.interpolate.html), especially the methods this method can take. **In order to use .interpolate(), the data type needs to be 'float'**, otherwise it will not work as expected.
 