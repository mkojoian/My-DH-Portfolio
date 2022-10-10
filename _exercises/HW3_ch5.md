---
layout: page
title: Exercises Chapter 5
description: Select exercises from Python Crash Course
---

```python
# 5.1
#1 
dog = 'labrador'
print("Is dog == 'labrador'?  I predict true.")
print(dog == 'labrador')

#2 
dog = 'labrador'
print("Is dog != 'labrador'?  I predict false.")
print(dog != 'labrador')

#3
book = 'A Christmas Carol'
print("Is book == 'a christmas carol'?  I predict false.")
print(book == 'a christmas carol')

#4
a = 144
b = 99
print("Is a == b?  I predict false")
print(a == b)

#5 
a = 144
b = 144
print("Is a == b?  I predict true")
print(a == b)

#6
a = 144
b = 144
print("Is a != b?  I predict false")
      
#7 
author = 'Mark Twain'
print("Is Mark Twain == Samuel Clemens?  I predict false")
print('Mark Twain' == 'Samuel Clemens')

#8 
canoe = 'Old Town'
print ("Is canoe = 'Old Town'?  I predict true")
print(canoe == "Old Town")

#9
paint = 'Acrylic'
paint.lower() == 'acrylic'
print("Is paint == 'Acrylic?' I predict true")
print(paint == 'Acrylic')

#10
paint = 'Acrylic'
paint.lower() == 'acrylic'
print("Is paint.lower == 'acrylic'  I predict true")
print(paint == 'Acrylic')
```

    True
    Is dog != 'labrador'?  I predict false.
    False
    Is book == 'a christmas carol'?  I predict false.
    False
    Is a == b?  I predict false
    False
    Is a == b?  I predict true
    True
    Is a != b?  I predict false
    Is Mark Twain == Samuel Clemens?  I predict false
    False
    Is canoe = 'Old Town'?  I predict true
    True
    Is paint == 'Acrylic?' I predict true
    True
    Is paint.lower == 'acrylic'  I predict true
    True



```python
# 5.2 
#1 Inequality test with strings
book = 'Little House on the Prairie'
print("Is book = 'little House on the Prairie'?  I predict false")
print(book == 'little House on the Prairie')

#2 Test using .lower()method
symphony = 'The Unfinished Symphony'
print("Is symphony.lower() = 'the unfinished symphony'?  I predict true")
print(symphony.lower() == 'the unfinished symphony')

#3 Numerical tests involving equality and inequality
x = 4
y = 16
print("Is x == y? I predict false")
print(x == y)

print("Is x <= y? I predict true")
print(x <= y)

print("Is x * 4 = y? I predict true")
print(x * 4 == y)

a = 4.5
b = 4
print("Is a < b?  I predict false")
print(a < b) 

#4 Tests using the and keyword and the or keyword
print("Is 15 = 15 or 16 == 15? I predict true")
print(15 == 15 or 16 == 15)

print("Is 15 == 16 or 15 == 17?  I predict false")
print(15 == 16 or 15 == 17)

print("Is 15 == 15 and 16 == 16?  I predict true")
print(15 == 15 and 16 == 16)

print("Is 15 == 15 and 15 == 16?  I predict false")
print(15 == 15 and 15 == 16)

#5 Test whether an item is in a list
composers = ['Mozart', 'Bach', 'Beethoven']
print("Is Schubert in composers?  I predict false")
print("Schubert" in composers)

print("Is Mozart in composers?  I predict true")
print("Mozart" in composers)

print("Is Chopin not in composers?  I predict true")
print("Chopin" not in composers)
```

    Is book = 'little House on the Prairie'?  I predict false
    False
    Is symphony.lower() = 'the unfinished symphony'?  I predict true
    True
    Is x == y? I predict false
    False
    Is x <= y? I predict true
    True
    Is x * 4 = y? I predict true
    True
    Is a < b?  I predict false
    False
    Is 15 = 15 or 16 == 15? I predict true
    True
    Is 15 == 16 or 15 == 17?  I predict false
    False
    Is 15 == 15 and 16 == 16?  I predict true
    True
    Is 15 == 15 and 15 == 16?  I predict false
    False
    Is Schubert in composers?  I predict false
    False
    Is Mozart in composers?  I predict true
    True
    Is Chopin not in composers?  I predict true
    True



```python
#5.6
age = 4
if age < 2:
    print("This is a baby.")
elif age >= 2 and age < 4:
    print("This is a toddler.")
elif age >= 4 and age < 13:
    print("This is a kid.")
elif age >= 13 and age < 20:
    print("This is a teenager.")
elif age >= 20 and age < 65:
    print("This is an adult.")
else: 
    print("This is an elder.")



```

    This is a kid.



```python
#5.7
favorite_fruits = ['blueberries', 'watermelon', 'raspberries', 'oranges']
if "banana" in favorite_fruits:
    print('you really like bananas')
    
if "blueberries" in favorite_fruits:
    print('you really like blueberries.')
    
if "watermelon" in favorite_fruits:
    print('you really like watermelon')
if "tangerine" in favorite_fruits:
    print('you really like  tangerines.')
if "oranges" in favorite_fruits:
    print('you really like oranges.')
```

    you really like blueberries.
    you really like watermelon
    you really like oranges.



```python
#5.8
usernames = ['admin', 'Ross', 'Bianca', 'Lucas', 'Jess']
for username in usernames:
    if username == 'admin':
        print('Hello, admin, would you like to see a status report?')
    else:
        print(f'Hello, {username}, thank you for logging in again.')
    
```

    Hello, admin, would you like to see a status report?
    Hello, Ross, thank you for logging in again.
    Hello, Bianca, thank you for logging in again.
    Hello, Lucas, thank you for logging in again.
    Hello, Jess, thank you for logging in again.



```python
#5.9
usernames = ['admin', 'Ross', 'Bianca', 'Lucas', 'Jess']
usernames.clear() #.clear() will clear a list.  
if usernames:
    for username in usernames:
        if username == 'admin':
            print('Hello, admin, would you like to see a progress report?')
        else:
            print(f'Hello, {username}, thank you for logging in again.')
        
else:
    print('We need to find some users.')
```

    We need to find some users.



```python
#5.10
current_users = ['angelica', 'jen', 'joe', 'phillip', 'abby']
new_users = ['Abby', 'Thomas', 'Phillip', 'Jackie']

for username in new_users:
    if username.lower() in current_users:
        print(f'{username} is not available as a username. Please choose a new one.')
    else:
        print(f'Thank you, {username}, this is an available username')
```

    Abby is not available as a username. Please choose a new one.
    Thank you, Thomas, this is an available username
    Phillip is not available as a username. Please choose a new one.
    Thank you, Jackie, this is an available username
    # End