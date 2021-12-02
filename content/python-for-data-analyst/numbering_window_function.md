---
title: "Numbering Window Function in Pandas and SQL"
date: 2021-11-18T00:07:26-07:00
draft: False
tags: ['Python', 'pandas', 'window functions', 'sql']
description: Window Functions in Pandas and SQL - Part 2. Numbering Window Function
---

This is not an introduction to window functions in Pandas or SQL. I will just use one example to compare implementing numbering window function in Pandas and Pandasql (SQL). 

RANK(), DENSE_RANK(), and ROW_NUMBER() are the three most common numbering functions in SQL, and I will just use RANK() for this demonstration. 

We can start from the following DataFrame *product*:
<table border="2" class="dataframe" style="width:80%">
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

If we want to perform a numbering operation to rank the sales for products in each category. In SQL (Pandasql), we can do:
```
q = """
    select 
        *
        , rank() over(
            partition by Category
            order by Sales desc
        ) as [Sales_rank]
    from product
"""
pysqldf(q)
```
to get the result DataFrame with ranks:
<table border="2" class="dataframe" style="width:80%">
  <thead>
    <tr style="text-align: right;">
      <th>Category</th>
      <th>Product_Name</th>
      <th>Sales</th>
      <th>Sales_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Cosmetics</td>
      <td>Lipstick</td>
      <td>50</td>
      <td>1</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Eyeliner</td>
      <td>20</td>
      <td>2</td>
    </tr>
    <tr>
      <td>Cosmetics</td>
      <td>Polish</td>
      <td>10</td>
      <td>3</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin D</td>
      <td>300</td>
      <td>1</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin C</td>
      <td>200</td>
      <td>2</td>
    </tr>
    <tr>
      <td>Food</td>
      <td>Vitamin A</td>
      <td>100</td>
      <td>3</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Conditioner</td>
      <td>700</td>
      <td>1</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Cream</td>
      <td>600</td>
      <td>2</td>
    </tr>
    <tr>
      <td>Personal Care</td>
      <td>Lotion</td>
      <td>500</td>
      <td>3</td>
    </tr>
  </tbody>
</table>

In Pandas we can use the following code to get exactly the same results:
```
product.join(
    (
        product
            .groupby('Category', as_index=False)['Sales']
            .rank(ascending=False)
    )
    , rsuffix='_rank'
).sort_values(by=['Category', 'Sales_rank'])
```
The Pandas code is not bad at all. If you are trying to manipulate the Pandas DataFrame with numbering window functions, especially dataset with a couple of millions rows, I would recommend sticking with Pandas instead of Pandasql. I will leave DENSE_RANK(), and ROW_NUMBER() for you to try out. Also, try to explore the difference between RANK() and DENSE_RANK(). [Pandas's rank() function](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rank.html) takes a **method** argument which allows you to specify whether you want dense_rank(). And the method='first' for Pandas's rank() is helpful if you want to assign row number to each row of a window. 

