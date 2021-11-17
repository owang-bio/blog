---
title: "Aggregate Window Functions in Pandas and SQL - draft"
date: 2021-11-02T00:37:30-06:00
draft: true
tags: ['Python', 'pandas', 'window functions', 'sql']
description: Window Functions in Pandas and SQL - Part 1 Aggregate Analytic Function
---

This is not an introduction to window functions. I will just use one example to compare implementing aggregate window function in Pandas and SQL. 

We can start from the following DataFrame df:
<table border="2" class="dataframe" style="width:60%">
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

If we want to perform a window operation to get the total sales for each category. In Pandas we can do:
```
product.assign(
    total_sales = product.groupby('Category')['Sales'].transform('sum')
)
```
and we will get:
<table border="2" class="dataframe" style="width:60%">
  <thead>
    <tr style="text-align: right;">
      <th>Category</th>
      <th>Product_Name</th>
      <th>Sales</th>
      <th>total_sales</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Supplement</td>
      <td>Vitamin A</td>
      <td>100</td>
      <td>600</td>
    </tr>
    <tr>
      <td>Supplement</td>
      <td>Vitamin C</td>
      <td>200</td>
      <td>600</td>
    </tr>
    <tr>
      <td>Supplement</td>
      <td>Vitamin D</td>
      <td>300</td>
      <td>600</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Lotion</td>
      <td>500</td>
      <td>1800</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Cream</td>
      <td>600</td>
      <td>1800</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Conditioner</td>
      <td>700</td>
      <td>1800</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Lipstick</td>
      <td>50</td>
      <td>80</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Eyeliner</td>
      <td>20</td>
      <td>80</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Polish</td>
      <td>10</td>
      <td>80</td>
    </tr>
  </tbody>
</table>
Of course, you can apply any aggregate functions, e.g. mean, std etc., over the window using the transform() method in Pandas. 

We get the same result with this SQL code:
```
q = """
    select 
        *
        , sum(Sales) over (
            partition by category
        ) as total_sales
    from product
"""

df = pysqldf(q)
```
If we use all the values in a window, and we don't have to sort the values. It is similar to use Pandas functions or SQL. In fact, Pandas solution might be a little bit easier. 



