---
layout: page
title: Variables, Strings, and Numbers
description: This notebook has code about variables, strings, and numbers
---

# Variables


```python
greeting = "Hello world!"
```


```python
print(greeting)
```

    Hello world!



```python
famous_person = "Emperor Augustus"
```


```python
print(famous_person)
```

    Emperor Augustus



```python
Virgil = "my dog"
print(Virgil)
```

    my dog



```python
ex_1 = "this is a string."
print(ex_1)
```

    this is a string.



```python
ex_2 = 'this is also a string.'
print(ex_2)
```

    this is also a string.



```python
ex_3 = "Here is a string.'
print(ex_3)
```


      Input In [8]
        ex_3 = "Here is a string.'
                                  ^
    SyntaxError: EOL while scanning string literal




```python
ex_4 = 'I asked the question, "when should I use single quotes."'
print(ex_4)
```

    I asked the question, "when should I use single quotes."



```python
historian = "edward gibbon"
print(historian)
```

    edward gibbon



```python
print(historian.title())
```

    Edward Gibbon



```python
print(historian.title)
```

    <built-in method title of str object at 0x7fc8f5e156f0>



```python
novelist = "Becky Chambers"
print(novelist.lower())
```

    becky chambers



```python
"becky" == "becky"
```




    True




```python
"becky" == "Becky"
```




    False




```python
first_name = "ada"
last_name = "lovelace"
```


```python
full_name = first_name + " " + last_name
print(full_name)
```

    ada lovelace



```python
greeting = f" Hello, {first_name} {last_name}"
print(greeting)
```

     Hello, ada lovelace



```python
print(greeting.title())
```

     Hello, Ada Lovelace


Whitespace


```python
print("tab")
```

    tab



```python
print("\ttab")
```

    	tab



```python
print("languages: \nGreek\nLatin\nEnglish")
```

    languages: 
    Greek
    Latin
    English



```python
str_rspace = "string      "
print(str_rspace)
```

    string      



```python
print(str_rspace.rstrip())
```

    string



```python
example = str_rspace + "."
print(example)
```

    string      .



```python
example_2 = str_rspace.rstrip() + "."
print(example_2)
```

    string.


#Numbers


```python
# integers
238 + 4834
```




    5072




```python
19 - 7
```




    12




```python
54 * 4
```




    216




```python
num = 45
```


```python
num + 5
```




    50




```python
type(num)
```




    int




```python
30 / 10 #This will return a float
```




    3.0




```python
#3.0 is a float instead of an integer
```


```python
f = 30 / 7
```


```python
f
```




    4.285714285714286




```python
type(f)
```




    float




```python
3 ** 3
```




    27



##Lists


```python
subjects = ['classics', 'history', 'philosophy']
```


```python
print(subjects)
```

    ['classics', 'history', 'philosophy']



```python
print(subjets[0])
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    Input In [27], in <cell line: 1>()
    ----> 1 print(subjets[0])


    NameError: name 'subjets' is not defined



```python
print(subjects[1])
```

    history



```python
print(subjects[-1])
```

    philosophy



```python
message = f"My most difficult subject is {subjects[1].title()}."
print(message)
```

    My most difficult subject is History.



```python
# how to modify a list
print(subjects)
```

    ['classics', 'history', 'philosophy']



```python
subjects[1] = 'sociology'
```


```python
print(subjects)
```

    ['classics', 'sociology', 'philosophy']



```python
# append
subjects.append('history')
print(subjects)
```

    ['classics', 'sociology', 'philosophy', 'history']



```python
subjects.append('mathematics')
print(subjects)
```

    ['classics', 'sociology', 'philosophy', 'history', 'mathematics']



```python
# insert
subjects.insert(0,'religion')
print(subjects)
```

    ['religion', 'classics', 'sociology', 'philosophy', 'history', 'mathematics']



```python
subjects.insert(2,'theology')
print(subjects)
```

    ['religion', 'classics', 'theology', 'sociology', 'philosophy', 'history', 'mathematics']



```python
# delete item
del subjects[-1]
print(subjects)
```

    ['religion', 'classics', 'theology', 'sociology', 'philosophy', 'history']



```python
# pop method
popped_subject = subjects.pop(0)
print(popped_subject)
print(subjects)
```

    religion
    ['classics', 'theology', 'sociology', 'philosophy', 'history']



```python
# remove
subjects.remove('sociology')
print(subjects)
```

    ['classics', 'theology', 'philosophy', 'history']



```python

```
