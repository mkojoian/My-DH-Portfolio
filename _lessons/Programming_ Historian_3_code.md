---
layout: page
title: Manipulating Strings in Python
description: This lesson teaches the user about Python strings
---


# Programming Historian Lesson 3: Manipulating Strings in Python


```python
message = "Hello World"
```

# Concatenating strings with the + sign


```python
message1 = 'hello' + ' ' + 'world'
print(message1)
```

    hello world


# Getting multiple copies of a string with the * sign


```python
message2a = 'hello ' * 3
message2b = 'world'
print(message2a + message2b)
```

    hello hello hello world


# Adding to the end of a string with the += sign (append)


```python
message3 = 'howdy'
message3 += ' '
message3 += 'world'
print(message3)
```

    howdy world


# Determining the length of a string (note that Python counts whitespace)


```python
message4 = 'hello' + ' ' + 'world'
print(len(message4))
```

    11


# Searching for a substring within a string (note that Python starts index from 0, not 1). 


```python
message5 = "hello world"
message5a = message5.find("worl")
print(message5a)
```

    6



```python
# If there is no substring to be found, Python will return -1.
```


```python
message6 = "Hello World"
message6b = message6.find("squirrel") # searching for a substring that does not exist in the string.
print(message6b)
```

    -1


# Standardizing Strings


```python
# The following code will change a string that includes capital letters to entirely lowercase type.
message7 = "HELLO WORLD"
message7a = message7.lower()
print(message7a)
# note that .upper can be used to the opposite effect adn .title will capitalize only the first letter of each word. 
```

    hello world


# Replacing letters in a string


```python
message8 = "HELLO WORLD"
message8a = message8.replace("L", "pizza") # every time there is an l, it will be replaced wiht the string "pizza."
print(message8a)
```

    HEpizzapizzaO WORpizzaD


# Slicing a string


```python
message9 = "Hello World"
message9a = message9[1:8] # Because indexing starts with 0, [1:8] cuts off the first character and everything after the 8th character.  The space is counted as a character.  
print(message9a)
```

    ello Wo



```python
# We can also assign variables to accomplish this same task.
startLoc = 2
endLoc = 8
message9b = message9[startLoc: endLoc]
print(message9b)
```

    llo Wo



```python
message9 = "Hello World"
print(message9[:5].find("d")) # nothing returned because there is no d in the first 6 (starts indexing at 0)characters of the string "Hello World."
```

    -1


# Escape Sequences


```python
print('\"')
# without the / Python would end the string too early (at the ").
```

    "



```python
print('The program printed \"hello world\"')
```

    The program printed "hello world"



```python
# Other escape sequences are \n and \t which make new lines and tabs respectively.  
print('hello\thello\thello\nworld')
```

    hello	hello	hello
    world

