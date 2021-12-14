---
title: "Sys Module"
date: 2021-12-02T23:30:19-07:00
draft: False
tags: ['python']
description: Brief Introduction to Python Sys Module
---

Python sys module provides access and functions to interaction with the interpreter, and I have used sys.argv and sys.path() the most frequently. 

### **1. sys.argv**
sys.argv allows users to pass command line arguments to python scripts. For example, if we have a simple python script, named as print_all.py:
```
import sys

for i in sys.argv[1:]:
    print(i)
```
If we execute this command in bash terminal:
```bash
python3 print_all.py 1 2 3
```
we will get:
```bash
1
2
3
```
The arguments can be a path to a file, an arguments needs to be passed to a function, or anything you need to manually input to a python script. It make running a python script much easier because you don't have to open it in any IDEs. 

### **2. sys.path()**
Sys module's sys.path.insert() and sys.path.append() add folders to the python environment temporarily, which allows me to import customized libraries/packages without the need to spell out the whole path for every package.
```
sys.path.insert(0, "/path_to_pythonModules/module1")
```
inserts module1 folder to the python environment, and it will be checked first when you are loading a package or running a script, while
```
sys.path.append("/path_to_pythonModules/module2")
```
add module2 folder to the last.

Sys module includes a lot more functions and allows users to access the other environment variables. If interested, you can read more details [here](https://docs.python.org/3/library/sys.html).