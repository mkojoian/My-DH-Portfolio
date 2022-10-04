---
layout: page
title: Exercises Ch. 3-4
description: Select exercises from Python Crash Course
---

```python
# 3.1
names = ["deirdre", "kelli", "andrew", "abby"]
print(names[0].title())
print(names[1].title())
print(names[2].title())
print(names[3].title())
```

    Deirdre
    Kelli
    Andrew
    Abby



```python
# 3.2
message = f"Hello, {names[0].title()}, how are you?"
print(message)
message = f"Hello, {names[1].title()}, how are you?"
print(message)
message = f"Hello, {names[2].title()}, how are you?"
print(message)
message = f"Hello, {names[3].title()}, how are you?"
print(message)
```

    Hello, Deirdre, how are you?
    Hello, Kelli, how are you?
    Hello, Andrew, how are you?
    Hello, Abby, how are you?



```python
# 3.3
cars = ["Ford", "Chevrolet", "GMC", "Buick"]
message = f"My favorite car is a {cars[0]}"
print(message)
message = f"My favorite car is a {cars[1]}"
print(message)
message = f"My favorite car is a {cars[2]}"
print(message)
message = f"My favorite car is a {cars[3]}"
print(message)
```

    My favorite car is a Ford
    My favorite car is a Chevrolet
    My favorite car is a GMC
    My favorite car is a Buick



```python
# 3.4
people = ["Marie Curie", "Euripides", "Neil Armstrong", "Thomas Jefferson"]
message = f"Hello, {people[0]} the cookout starts at 5:30.  Will you be there?"
print(message)
message = f"Hello, {people[1]} the cookout starts at 5:30.  Will you be there?"
print(message)
message = f"Hello, {people[2]} the cookout starts at 5:30.  Will you be there?"
print(message)
message = f"Hello, {people[3]} the cookout starts at 5:30.  Will you be there?"
print(message)
```

    Hello, Marie Curie the cookout starts at 5:30.  Will you be there?
    Hello, Euripides the cookout starts at 5:30.  Will you be there?
    Hello, Neil Armstrong the cookout starts at 5:30.  Will you be there?
    Hello, Thomas Jefferson the cookout starts at 5:30.  Will you be there?



```python
# 3.5
print(people[1])
people[1] = "Bruce Lee"
print(people)
message = f"Hello, {people[0]} I've had a few cancelations, but dinner is still at 5:30.  Will you be there?"
print(message)
message = f"Hello, {people[1]} I've had a few cancelations, but dinner is still at 5:30.  Will you be there?"
print(message)
message = f"Hello, {people[2]} I've had a few cancelations, but dinner is still at 5:30.  Will you be there?"
print(message)
message = f"Hello, {people[3]} I've had a few cancelations, but dinner is still at 5:30.  Will you be there?"
print(message)
```

    Euripides
    ['Marie Curie', 'Bruce Lee', 'Neil Armstrong', 'Thomas Jefferson']
    Hello, Marie Curie I've had a few cancelations, but dinner is still at 5:30.  Will you be there?
    Hello, Bruce Lee I've had a few cancelations, but dinner is still at 5:30.  Will you be there?
    Hello, Neil Armstrong I've had a few cancelations, but dinner is still at 5:30.  Will you be there?
    Hello, Thomas Jefferson I've had a few cancelations, but dinner is still at 5:30.  Will you be there?



```python
# 3.6
print("I found a new table to use for tonight's cookout. See you there!")
people.insert(0, "Gilgamesh")
print(people)
people.insert(3, "Khafre")
print(people)
people.append("Menkaure")
print(people)
message = f"Hello, {people[0]} the cookout starts at 5:30. Will you be there?"
print(message)
message = f"Hello, {people[1]} the cookout starts at 5:30. Will you be there?"
print(message)
message = f"Hello, {people[2]} the cookout starts at 5:30. Will you be there?"
print(message)
message = f"Hello, {people[3]} the cookout starts at 5:30. Will you be there?"
print(message)
message = f"Hello, {people[4]} the cookout starts at 5:30. Will you be there?"
print(message)
message = f"Hello, {people[5]} the cookout starts at 5:30. Will you be there?"
print(message)
message = f"Hello, {people[6]} the cookout starts at 5:30. Will you be there?"
print(message)
```

    I found a new table to use for tonight's cookout. See you there!
    ['Gilgamesh', 'Marie Curie', 'Bruce Lee', 'Neil Armstrong', 'Thomas Jefferson']
    ['Gilgamesh', 'Marie Curie', 'Bruce Lee', 'Khafre', 'Neil Armstrong', 'Thomas Jefferson']
    ['Gilgamesh', 'Marie Curie', 'Bruce Lee', 'Khafre', 'Neil Armstrong', 'Thomas Jefferson', 'Menkaure']
    Hello, Gilgamesh the cookout starts at 5:30. Will you be there?
    Hello, Marie Curie the cookout starts at 5:30. Will you be there?
    Hello, Bruce Lee the cookout starts at 5:30. Will you be there?
    Hello, Khafre the cookout starts at 5:30. Will you be there?
    Hello, Neil Armstrong the cookout starts at 5:30. Will you be there?
    Hello, Thomas Jefferson the cookout starts at 5:30. Will you be there?
    Hello, Menkaure the cookout starts at 5:30. Will you be there?



```python
# 3.7
message = "I can only have two people for dinner tonight."
print(message)
popped_people = people.pop()
print(people)
print(f"I have to retract my earlier invitation, {popped_people}. There is no more room at my small table.")
popped_people = people.pop()
print(people)
print(f"I have to retract my earlier invitation, {popped_people}. There is no more room at my small table.")  
popped_people = people.pop()
print(people)
print(f"I have to retract my earlier invitation, {popped_people}. There is no more room at my small table.")  
popped_people = people.pop()
print(people)
print(f"I have to retract my earlier invitation, {popped_people}. There is no more room at my small table.")  
popped_people = people.pop()
print(people)
print(f"I have to retract my earlier invitation, {popped_people}. There is no more room at my small table.") 

      
print(f"Hello, {people[0]}. You made the final cut and are still invited to my cookout tonight.")
print(f"Hello, {people[1]}. You made the final cut and are still invited to my cookout tonight.")
      
del people[0]
del people[0]
print(people)
```

    I can only have two people for dinner tonight.
    ['Gilgamesh', 'Marie Curie', 'Bruce Lee', 'Khafre', 'Neil Armstrong', 'Thomas Jefferson']
    I have to retract my earlier invitation, Menkaure. There is no more room at my small table.
    ['Gilgamesh', 'Marie Curie', 'Bruce Lee', 'Khafre', 'Neil Armstrong']
    I have to retract my earlier invitation, Thomas Jefferson. There is no more room at my small table.
    ['Gilgamesh', 'Marie Curie', 'Bruce Lee', 'Khafre']
    I have to retract my earlier invitation, Neil Armstrong. There is no more room at my small table.
    ['Gilgamesh', 'Marie Curie', 'Bruce Lee']
    I have to retract my earlier invitation, Khafre. There is no more room at my small table.
    ['Gilgamesh', 'Marie Curie']
    I have to retract my earlier invitation, Bruce Lee. There is no more room at my small table.
    Hello, Gilgamesh. You made the final cut and are still invited to my cookout tonight.
    Hello, Marie Curie. You made the final cut and are still invited to my cookout tonight.
    []



```python
# 3.8
places = ["Italy", "Greece", "France", "Iceland", "Spain"]
print(places)
print(sorted(places))
print(places)
places.reverse()
print(places)
places.reverse()
print(places)
places.sort()
print(places)
places.sort(reverse = True)
print(places)
```

    ['Italy', 'Greece', 'France', 'Iceland', 'Spain']
    ['France', 'Greece', 'Iceland', 'Italy', 'Spain']
    ['Italy', 'Greece', 'France', 'Iceland', 'Spain']
    ['Spain', 'Iceland', 'France', 'Greece', 'Italy']
    ['Italy', 'Greece', 'France', 'Iceland', 'Spain']
    ['France', 'Greece', 'Iceland', 'Italy', 'Spain']
    ['Spain', 'Italy', 'Iceland', 'Greece', 'France']



```python
# Chapter 4
# 4.1
pizzas = ["veggie", "cheese", "pepperoni"]
for pizza in pizzas:
    print(pizza.title())
for pizza in pizzas:
    print(f"My favorite kind of pizza is {pizza}.")
print("I like making pizzas too.")
```

    Veggie
    Cheese
    Pepperoni
    My favorite kind of pizza is veggie.
    My favorite kind of pizza is cheese.
    My favorite kind of pizza is pepperoni.
    I like making pizzas too.



```python
# 4.2
animals = ["giraffes", "elephants", "zebras"]
for animal in animals:
    print(animal)
for animal in animals:
    print(f"{animal.title()} live in the jungle.")
print("I like jungle animals.")
```

    giraffes
    elephants
    zebras
    Giraffes live in the jungle.
    Elephants live in the jungle.
    Zebras live in the jungle.
    I like jungle animals.



```python
# 4.3
for numbers in range(1,21):
    print(numbers)
```

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20



```python
# 4.6
for odd_numbers in range(1,21,2):
    print(odd_numbers)
```

    1
    3
    5
    7
    9
    11
    13
    15
    17
    19



```python
# 4.10
woods = ["maple", "oak", "pine", "spruce", "cedar"]
print(f"The first three items in the list are:")
for wood in woods[0:3]:
    print(wood)
print("Three items from the middle of the list are:")
for wood in woods [1:4]:
    print(wood)
print("The last three items in the list are:")
for wood in woods[2:5]:
    print(wood)

```

    The first three items in the list are:
    maple
    oak
    pine
    Three items from the middle of the list are:
    oak
    pine
    spruce
    The last three items in the list are:
    pine
    spruce
    cedar



```python
# 4.11
my_pizzas = ["veggie", "cheese", "pepperoni"]
friend_pizzas = my_pizzas[:]
my_pizzas.append("white")
print(my_pizzas)
friend_pizzas.append("meatball")
print(friend_pizzas)
print("\nMy favorite pizzas are:")
for pizza in my_pizzas:
    print(pizza)
print("\nMy firend's favorite pizzas are:")
for pizza in friend_pizzas:
    print(pizza)
```

    ['veggie', 'cheese', 'pepperoni', 'white']
    ['veggie', 'cheese', 'pepperoni', 'meatball']
    
    My favorite pizzas are:
    veggie
    cheese
    pepperoni
    white
    
    My firend's favorite pizzas are:
    veggie
    cheese
    pepperoni
    meatball



```python
# End
```
