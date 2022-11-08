---
layout: page
title: Working with Text Files in Python
description: Programming Historian 2 Code
---

# Programming Historian Lesson 2 "Working with Text Files in Python"


```python
# file-output.py
f = open('helloworld.txt','w')
f.write('hello world')
f.close()
# The w means that I intend to write content to this new file using Python.
```


```python
# file-input.py
f = open('helloworld.txt','r')
message = f.read()
print(message)
f.close()
# the r parameter means I am opening a file to read from it.  The result is below.  
```

    hello world



```python
# file-append.py
f = open('helloworld.txt','a')
f.write('\n' + 'hello world')
f.close()
# Run this cell a few times and then re-check the helloworld.txt file.  There should be more "hello worlds" added to the original one.  
# Open, write, and close are methods (i.e. "bits of code which perform actions.  
# They do something to something else and return a result")
```
