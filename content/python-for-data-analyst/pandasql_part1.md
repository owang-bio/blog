---
title: "Pandasql"
date: 2021-11-01T01:16:30-06:00
draft: False
tags: ['Python', 'Pandas','sql']
description: Introducing pandasql
---

It would be nice if we can write SQL code to query Pandas DataFrame, and pandasql is just the package that enabled this. It accepts sqlite syntax and it support window functions. In this post, I am going to demonstrate why sometimes it is more convenient to write sql queries than calling Pandas functions. 

First, let's set up some DataFrame, department:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Operations</td>
    </tr>
    <tr>
      <td>2</td>
      <td>IT</td>
    </tr>
    <tr>
      <td>3</td>
      <td>HR</td>
    </tr>
  </tbody>
</table>

and employee:
<table border="2" class="dataframe" style="width:40%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>name</th>
      <th>department_id</th>
      <th>salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>John</td>
      <td>2</td>
      <td>60000</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Mike</td>
      <td>3</td>
      <td>80000</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Jane</td>
      <td>2</td>
      <td>70000</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Gabi</td>
      <td>3</td>
      <td>90000</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Amy</td>
      <td>1</td>
      <td>60000</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Tim</td>
      <td>1</td>
      <td>50000</td>
    </tr>
  </tbody>
</table>

Let's say if we are trying to figure out the average and total salary for each department. 

### **1. Do it in Pandas**
This would be my code:
```
(
    department.merge(
        employee
        , left_on='id'
        , right_on='department_id'
        , how='inner'
        , suffixes=['_department', '_employee']
    )
        .groupby(['id_department', 'name_department'], as_index=False)
        .agg(
            average_salary = pd.NamedAgg(column='salary', aggfunc='mean')
            , total_salary = pd.NamedAgg(column='salary', aggfunc='sum')
        )
        .rename(
            columns={
                'id_department':'id'
                , 'name_department':'name'
            }
        )
)
```
and we get:
<table border="2" class="dataframe" style="width:40%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>name</th>
      <th>average_salary</th>
      <th>total_salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Operations</td>
      <td>55000</td>
      <td>110000</td>
    </tr>
    <tr>
      <td>2</td>
      <td>IT</td>
      <td>65000</td>
      <td>130000</td>
    </tr>
    <tr>
      <td>3</td>
      <td>HR</td>
      <td>85000</td>
      <td>170000</td>
    </tr>
  </tbody>
</table>
It is a lot of code, but more importantly it is hard to read.

### **2. Do it with pandasql**
Now, let's do it with pandasql:
```
from pandasql import sqldf
pysqldf = lambda q: sqldf(q, globals())
```
It is easier to set a function that takes a sql query than to provide a globals() arguments every time. The second argument can also be locals() if you are using it within a function. In fact, the second argument is just a dictionary, you can create it with keys as table names in the sql query and python variable name as the elements. 

The sql query should be very simple for this task:
```
q = """
    select 
        d.id
        , d.name
        , avg(e.salary) as average_salary
        , sum(e.salary) as total_salary        
    from department d
    join employee e
        on d.id = e.department_id
    group by d.id
"""

pysqldf(q)
```
and we get exact the same results in the above:
<table border="2" class="dataframe" style="width:30%">
  <thead>
    <tr style="text-align: right;">
      <th>id</th>
      <th>name</th>
      <th>average_salary</th>
      <th>total_salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Operations</td>
      <td>55000.0</td>
      <td>110000</td>
    </tr>
    <tr>
      <td>2</td>
      <td>IT</td>
      <td>65000.0</td>
      <td>130000</td>
    </tr>
    <tr>
      <td>3</td>
      <td>HR</td>
      <td>85000.0</td>
      <td>170000</td>
    </tr>
  </tbody>
</table>
Much less code and more clear to read! One thing to note is that when the we are dealing with large volume of data (can't be too large though if we are using Pandas), the performance of Pandasql is not very good.