---
title: "String Functions in Pandas"
date: 2021-10-26T22:56:13-06:00
draft: False
tags: ['Python', 'Pandas', 'String Functions']
description: String Methods, replace(), contains(), extract() and match() in Pandas
---

To apply to string methods to strings in a Pandas Series, a **.str** accessor is needed. For example:
```
Series.str.lower()
```
This post will focus on four string methods, replace(), contains(), extract(), match(). The more comprehensive list of string methods for Pandas Series can be find [here](https://pandas.pydata.org/docs/reference/series.html#api-series-str). In my opinion, in order to use extract() and match(), a basic understanding of regular expression is a must. 

We can create a Series (variable named as demo_str) of string to demonstrate these functions:
```
0 Pepsi 8oz
1 Coke 236ml
2 Diet Coke 8oz
3 Diet Pepsi 236ml
Name: Drinks, dtype: object
```

### **1. replace()**
They syntax is fairly simple. For example, if we want to replace "236ml" with "8oz", we can do:
```
demo_str.str.replace('236ml', '8oz')
```
and now we get:
```
0         Pepsi 8oz
1          Coke 8oz
2     Diet Coke 8oz
3    Diet Pepsi 8oz
Name: Drinks, dtype: object
```
One argument **case** takes boolean type to specify whether it is case sensitive. Another argument **regex** can be used to to specify if the pattern is a regular expression. 

For example:
```
demo_str.str.replace('\d+ML', '8oz', case=False, regex=True)
```
will also replace "236ml" to "8oz". In fact, it will change any number followed by "ml", "mL", "Ml" or "ML" to "8oz". Regular expression is a big topic, I will introduce it in future posts.

### **2. contains()**
Contain tests if the string contains a pattern, and it takes a pattern argument, for example, if we want to test if "pepsi" is contained in any of the strings (and case insensitive), we can do:
```
demo_str.str.contains(pat='pepsi', case=False)
```
which will return:
```
0     True
1    False
2    False
3     True
Name: Drinks, dtype: bool
```
contains() also support regular expression, and I often use contains() and match() interchangeably when the pattern is a regular expression. But when the pattern is not a regular expression, I often use contains() because it detect the patterns in any positions of a string while match() test the matches from the beginning of a string. 

### **3. extract()**
extract() returns a Series or DataFrame depending on how many groups we are trying to extract. The group(s) of patterns that we are trying to extract need to be included in a pair of parenthesis "()". For example, if I am trying to extract the number from the demo_str series, I can do:
```
demo_str.str.extract('(\d+)')
```
This will give me a Series, because I only have one pair of parenthesis:
```
0      8
1    236
2      8
3    236
Name: Drinks, dtype: object
```
Now if I want to extract the number and their units, with two pairs of parenthesis:
```
demo_str.str.extract('[^0-9]*(\d+)([a-zA-Z]+)')
```
I will get a DataFrame with 2 columns:
```
|   0 | 1   |
|:----|:----|
|   8 | oz  |
| 236 | ml  |
|   8 | oz  |
| 236 | ml  |
```
The first column includes the first group which is the number, and the second column includes the second group which is the units. 

### **3. match()**
match() will test if the pattern exists in a string at start of the string. For example:
```
demo_str.str.match(pat='pepsi', case=False)
```
will return
```
0     True
1    False
2    False
3    False
Name: Drinks, dtype: bool
```
The forth record returned a False because it did not start with "Pepsi". However, if we do this with a regular expression:
```
demo_str.str.match(pat='.*pepsi', case=False)
```
we will get the same results as contains():
```
0     True
1    False
2    False
3     True
Name: Drinks, dtype: bool
```
Match also take the case argument, similar to contains().

### **4. Chain up The String Methods**
The string methods can be chained up, but each time a method is called, .str accessor is needed. For example, if we want to replace "ml" to "oz", and then extract their volume and units, we can do:
```
(
    demo_str.str.replace('236ml', '8oz')
        .str.extract('[^0-9]*(\d+)([a-zA-Z]+)')
)
```
and we will get a DataFrame with updated values:
```
|   0 | 1   |
|----:|:----|
|   8 | oz  |
|   8 | oz  |
|   8 | oz  |
|   8 | oz  |
```