---
title: "Aggregate Window Functions in Pandas and SQL"
date: 2021-11-02T00:37:30-06:00
draft: False
tags: ['Python', 'pandas', 'window functions', 'sql']
description: Window Functions in Pandas and SQL - Part 1 Aggregate Analytic Function
---

This is not an introduction to window functions in Pandas or SQL. I will just use one example to compare implementing aggregate window function in Pandas and SQL. 

We can start from the following DataFrame df with 3 groups and some random values:
<table border="2" class="dataframe" style="width:40%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>group</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>a</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>a</td>
      <td>2</td>
    </tr>
    <tr>
      <td>2</td>
      <td>a</td>
      <td>3</td>
    </tr>
    <tr>
      <td>3</td>
      <td>a</td>
      <td>4</td>
    </tr>
    <tr>
      <td>4</td>
      <td>a</td>
      <td>5</td>
    </tr>
    <tr>
      <td>5</td>
      <td>a</td>
      <td>6</td>
    </tr>
    <tr>
      <td>6</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <td>7</td>
      <td>b</td>
      <td>2</td>
    </tr>
    <tr>
      <td>8</td>
      <td>b</td>
      <td>3</td>
    </tr>
    <tr>
      <td>9</td>
      <td>b</td>
      <td>4</td>
    </tr>
    <tr>
      <td>10</td>
      <td>b</td>
      <td>5</td>
    </tr>
    <tr>
      <td>11</td>
      <td>b</td>
      <td>6</td>
    </tr>
    <tr>
      <td>12</td>
      <td>c</td>
      <td>1</td>
    </tr>
    <tr>
      <td>13</td>
      <td>c</td>
      <td>2</td>
    </tr>
    <tr>
      <td>14</td>
      <td>c</td>
      <td>3</td>
    </tr>
    <tr>
      <td>15</td>
      <td>c</td>
      <td>4</td>
    </tr>
    <tr>
      <td>16</td>
      <td>c</td>
      <td>5</td>
    </tr>
    <tr>
      <td>17</td>
      <td>c</td>
      <td>6</td>
    </tr>
  </tbody>
</table>

If I am getting the sum of a 4 rows window with 1 row before current row and 2 row after, this would be my code to perform it in Pandasql:

```
q = """
    select
        *
        , sum(value) over(
            partition by [group]
            order by id
            rows between 1 preceding and 2 following
        ) as value_new
    from df
"""
pysqldf(q)
``` 
Not a lot difficult to achieve this if we do it with SQL. Now let's do it in Pandas:

```
n_follow = 2
n_preced = 1
n_window = n_follow + n_preced + 1

df_part_1 = (
    df
        .sort_values(by='id')
        .groupby('group', as_index=False)
        .apply(lambda x:pd.concat([x.head(n_follow), x.shift(-n_follow)]))
        .reset_index()
        .set_index('level_1')
)

df_part_1['group'].fillna(method='pad', inplace=True)

df_part_2 = (
    df_part_1        
        .groupby('group', as_index=False)['value']
        .rolling(n_window, min_periods=1)
        .sum()
        .groupby('group', as_index=False)
        .apply(lambda x:x[n_follow:])
        .reset_index()
        .set_index('level_1')
        ['value']
)

result = df.join(df_part_2, rsuffix='_new')
```
Both approaches return the same results:

<table border="2" class="dataframe" style="width:40%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>group</th>
      <th>value</th>
      <th>value_new</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>a</td>
      <td>1</td>
      <td>6.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>a</td>
      <td>2</td>
      <td>10.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>a</td>
      <td>3</td>
      <td>14.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>a</td>
      <td>4</td>
      <td>18.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>a</td>
      <td>5</td>
      <td>15.0</td>
    </tr>
    <tr>
      <td>5</td>
      <td>a</td>
      <td>6</td>
      <td>11.0</td>
    </tr>
    <tr>
      <td>6</td>
      <td>b</td>
      <td>1</td>
      <td>6.0</td>
    </tr>
    <tr>
      <td>7</td>
      <td>b</td>
      <td>2</td>
      <td>10.0</td>
    </tr>
    <tr>
      <td>8</td>
      <td>b</td>
      <td>3</td>
      <td>14.0</td>
    </tr>
    <tr>
      <td>9</td>
      <td>b</td>
      <td>4</td>
      <td>18.0</td>
    </tr>
    <tr>
      <td>10</td>
      <td>b</td>
      <td>5</td>
      <td>15.0</td>
    </tr>
    <tr>
      <td>11</td>
      <td>b</td>
      <td>6</td>
      <td>11.0</td>
    </tr>
    <tr>
      <td>12</td>
      <td>c</td>
      <td>1</td>
      <td>6.0</td>
    </tr>
    <tr>
      <td>13</td>
      <td>c</td>
      <td>2</td>
      <td>10.0</td>
    </tr>
    <tr>
      <td>14</td>
      <td>c</td>
      <td>3</td>
      <td>14.0</td>
    </tr>
    <tr>
      <td>15</td>
      <td>c</td>
      <td>4</td>
      <td>18.0</td>
    </tr>
    <tr>
      <td>16</td>
      <td>c</td>
      <td>5</td>
      <td>15.0</td>
    </tr>
    <tr>
      <td>17</td>
      <td>c</td>
      <td>6</td>
      <td>11.0</td>
    </tr>
  </tbody>
</table>

Now let's talk about the Pandas code (BTW, it is confusing and you can skip it and read the last paragraph). I haven't found a flexible window function in Pandas that lets the user specify the range of rows to perform a aggregate function to. So I need to calculate the number of values in each window which is "n_precedding + n_following + 1 (current row)". Then I shift the DataFrame up by the number of rows after the current row (n_following), so that we can use rolling() function in pandas to include everything in that window. shift() in pandas will remove the values on the top, so I then concatenate the head (n_following) rows back to the top for each partition. When the rows were shifted up, missing values were created, so I need to fill the missing value with the last valid number with fillna(method='pad') function. After aggregation is performed, the top rows needed to be removed because they were concatenated after the shift. I kept tracking the original index, because at the final step, I need to join the results back with the original DataFrame by index. That's the algorithm I used with Pandas to perform an aggregate window function... 

This is just an very general example, and I can just follow these steps to perform a lot of aggregate window functions with Pandas. Of course, Pandas provides several types of [windows](https://pandas.pydata.org/pandas-docs/stable/user_guide/window.html) and methods, such as [transform()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.transform.html), to make our life a little easier. However, I would still recommend just using pandasql or similar library (such as dataframe_sql) for this purposes because SQL code is way easier to read.  
