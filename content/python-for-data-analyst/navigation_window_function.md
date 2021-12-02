---
title: "Navigation Window Function in Pandas and SQL"
date: 2021-11-21T01:09:11-07:00
draft: True
tags: ['Python', 'pandas', 'window functions', 'sql']
description: Window Functions in Pandas and SQL - Part 3. Navigation Function
---

This is not an introduction to window functions in Pandas or SQL. I will just use some examples to compare implementing navigation window function in Pandas and Pandasql (SQL). Navigation window function returns value at a specific row of a window. For example, the first, last, n_th value, or next/last value relative to the current row. 

We can start from the following DataFrame ***product***:
<table border="2" class="dataframe" style="width:70%">
  <thead>
    <tr style="text-align: right;">
      <th>Category</th>
      <th>Product_Name</th>
      <th>Sales</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Supplement</td>
      <td>Vitamin A</td>
      <td>100</td>
    </tr>
    <tr>
      <td>Supplement</td>
      <td>Vitamin C</td>
      <td>200</td>
    </tr>
    <tr>
      <td>Supplement</td>
      <td>Vitamin D</td>
      <td>300</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Lotion</td>
      <td>500</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Cream</td>
      <td>600</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Conditioner</td>
      <td>700</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Lipstick</td>
      <td>50</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Eyeliner</td>
      <td>20</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Polish</td>
      <td>10</td>
    </tr>
  </tbody>
</table>

If we are getting the product name of the second best selling product, with SQL (Pandasql) we can do:
```
q = """
    select
        *
        , nth_value(Product_Name, 2) over(
            partition by Category
            order by Sales desc
            rows between unbounded preceding and unbounded following
        ) as Second_prod
    from product
"""
pysqldf(q)
```
and we will get:
<table border="2" class="dataframe" style="width:80%">
  <thead>
    <tr style="text-align: right;">
      <th>Category</th>
      <th>Product_Name</th>
      <th>Sales</th>
      <th>Second_prod</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Cosmetics</td>
      <td>Lipstick</td>
      <td>50</td>
      <td>Eyeliner</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Eyeliner</td>
      <td>20</td>
      <td>Eyeliner</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Polish</td>
      <td>10</td>
      <td>Eyeliner</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin D</td>
      <td>300</td>
      <td>Vitamin C</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin C</td>
      <td>200</td>
      <td>Vitamin C</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin A</td>
      <td>100</td>
      <td>Vitamin C</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Conditioner</td>
      <td>700</td>
      <td>Cream</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Cream</td>
      <td>600</td>
      <td>Cream</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Lotion</td>
      <td>500</td>
      <td>Cream</td>
    </tr>
  </tbody>
</table>
If we do it in Pandas, to get the same above results, this would be my code:

```
product.merge(
    (
        product
            .sort_values(by='Sales', ascending=False)
            .groupby('Category')['Product_Name']
            .nth(1)
            .reset_index()
    )
    , on='Category'
    , suffixes=['', '_r']
).rename(
    columns={
        'Product_Name_r':'Second_prod'
    }
)
```
It is not too bad, and I would stick with Pandas if I need to perform navigation window functions with Pandas DataFrame. The nth() function starts with 0, which is why when we need the second value, the input was 1. Also, Pandas's output does not include every row in the original DataFrame, and thus we need to merge() it with the original dataset to get the same output as window functions in SQL.

Lag() and Lead() are two commonly used navigation functions in SQL to get the previous and next value, respectively. In Pandas, Lag() and Lead() can be implemented using shift(-1) and shift(1). For example:

```
q = """
    select 
        *
        , lag(Product_Name) over(
            partition by Category
            order by Sales desc
            rows between unbounded preceding and unbounded following
        ) as Second_prod
    from product
"""
pysqldf(q)
``` 

and 

```
product.join(
    (
        product
            .sort_values(by='Sales', ascending=False)
            .groupby('Category')['Product_Name']
            .shift(1)
    )
    , rsuffix='_r'
).rename(
    columns={
        'Product_Name_r':'Second_prod'
    }
).sort_values(by=['Category', 'Sales'], ascending=False)
```
will return the same results:
<table border="2" class="dataframe" style="width:80%">
  <thead>
    <tr style="text-align: right;">
      <th>Category</th>
      <th>Product_Name</th>
      <th>Sales</th>
      <th>Second_prod</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Personal Care</td>
      <td>Conditioner</td>
      <td>700</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Cream</td>
      <td>600</td>
      <td>Conditioner</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Lotion</td>
      <td>500</td>
      <td>Cream</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin D</td>
      <td>300</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin C</td>
      <td>200</td>
      <td>Vitamin D</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin A</td>
      <td>100</td>
      <td>Vitamin C</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Lipstick</td>
      <td>50</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Eyeliner</td>
      <td>20</td>
      <td>Lipstick</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Polish</td>
      <td>10</td>
      <td>Eyeliner</td>
    </tr>
  </tbody>
</table>

To sum up, I think it is both convenient to perform navigation functions with Pandasql (or dataframe_sql) and Pandas builtin functions. But when the DataFrame has millions of rows, I would stick with Pandas functions or dataframe_sql, because Pandasql relies on SQLite which is slow when the DataFrame gets big. 