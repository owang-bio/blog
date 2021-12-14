---
title: "OS Module"
date: 2021-12-03T08:49:52-07:00
draft: False
tags: ['python']
description: Brief Introduction to Python OS Module
---

Python os module provides functions to interact with directories and files. I use the following 3 functions most frequently.

### **1. os.getcwd()**
os.getcwd() returns a string of the current working directory. 

### **2. list files in a directory**
os.listdir(path_to_directory) returns a list, and the elements are the files inside of the directory. 

### **3. Test if a file is a directory**
os.path.isfile(path) returns ***True*** of ***False*** depending on if the path is to a directory or not.

One example of how I use os module. I often need to process multiple .csv files, and I usually load them with Pandas, make some changes, and then save it back. My Python code would be like:
```
for file in os.listdir(path_to_directory):
    if re.search(r'.*\.csv', file):
        do something
        save to csv
```