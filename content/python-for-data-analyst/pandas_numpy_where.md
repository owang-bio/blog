---
title: "Pandas where() vs Numpy.where()"
date: 2021-10-29T00:44:08-06:00
draft: true
tags: ['Python', 'pandas', 'numpy', 'where']
description: Difference between pandas where() and numpy.where(), also introduction of pandas mask()
--- 

Pandas and Numpy have lots of methods that share the same name, and where() is one of them that I use very often. They are both if...else... function, but there are some difference and this post will explain the difference. 

Let's start with a DataFrame (df):
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <td>3</td>
      <td>4</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5</td>
    </tr>
  </tbody>
</table>

Pandas where() is a Series method, and the first positional keyword "cond" takes a condition. If the condition is True, the original value is kept, otherwise by default it returns NA. For example:
```
df['value'].where(df['value'] > 3)
```
returns:
```
0    NaN
1    NaN
2    NaN
3    4.0
4    5.0
Name: value, dtype: float64
```
We can provide a value to replace the original values when the condition is not met:
```
df['value'].where(df['value'] > 3, 999)
```
and it gives us:
```
0    999
1    999
2    999
3      4
4      5
Name: value, dtype: int64
```
If we set the **"inplace"** argument to True, the where() method becomes an setter and the values in the DataFrame will be changed. Pandas **mask()** is just the opposite of where(): when condition is not met, the values will be replaced, otherwise we can treat where() as an accessor for Pandas DataFrame. 

Numpy.where() takes at least 3 arguments: cond, x and y. x and y can be Numpy Array or Pandas Series, or anything that is [broadcastable](https://www.tutorialspoint.com/numpy/numpy_broadcasting.htm). For Numpy.where(), when condition is met, the value is taken from x, otherwise from y. For example:
```
np.where(df['value'] > 3, 1, 0)
```
will return:
```
array([0, 0, 0, 1, 1])
```
Notice numpy.where() returns an array, which can be directly assigned to pandas DataFrame as a column:
```
df['Greater Than 3'] = np.where(df['value'] > 3, 1, 0)
```
will return a DataFrame with 3 columns:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>value</th>
      <th>Greater Than 3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3</td>
      <td>0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
That's all about the difference between Pandas and Numpy where(). When you are changing the original values even the condition is met, I would recomand using numpy.where()