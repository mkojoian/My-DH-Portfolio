---
layout: page
title: Exercises Chapter 6
description: Select exercises from Python Crash Course
---

```python
# 6.1
person = {'first_name': 'Abigail', 'last_name': 'Kojoian', 'age': '21', 'city': 'Cranston'}
print(person['first_name'])
print(person['last_name'])
print(person['age'])
print(person['city'])
```

    Abigail
    Kojoian
    21
    Cranston



```python
# 6.2
favorite_numbers = {'Abigail': '4', 'Elena': '21', 'Kevin': '5', 'Camp': '3', 'Marilyn': '33'}
print(favorite_numbers['Abigail'])
print(favorite_numbers['Elena'])
print(favorite_numbers['Kevin'])
print(favorite_numbers['Camp'])
print(favorite_numbers['Marilyn'])
```

    4
    21
    5
    3
    33



```python
# 6.3
python_words = {
    'for_loops': 'iterate through data', 
    'variables': 'assign a value', 
    'if_statements': 'set up a condition',
    'print_commands': 'print an output', 
    'lists': 'store multiple items in a single variable',
    }


print(f"for_loops: {python_words['for_loops']}.")

print(f"\nvariables: {python_words['variables']}.")

print(f"\nif_statements: {python_words['if_statements']}.")

print(f"\nprint_commands: {python_words['print_commands']}.")

print(f"\nlists: {python_words['lists']}.")
```

    for_loops: iterate through data.
    
    variables: assign a value.
    
    if_statements: set up a condition.
    
    print_commands: print an output.
    
    lists: store multiple items in a single variable.



```python
# 6.4

for key, value in python_words.items():
    print(f"\nKey: {key}")
    print(f"Value: {value}")

```

    
    Key: for_loops
    Value: iterate through data
    
    Key: variables
    Value: assign a value
    
    Key: if_statements
    Value: set up a condition
    
    Key: print_commands
    Value: print an output
    
    Key: lists
    Value: store multiple items in a single variable



```python
# 6.5
rivers = {'nile': 'egypt', 'tiber': 'italy', 'yangtze': 'china'}
for river, country in rivers.items():
    print(f"The {river.title()} is located in {country.title()}.")

for river, country in rivers.items():
    print(f"\nRivers: {river.title()}")

for country in rivers.values():
    print(f"\nCountry: {country.title()}")
  
```

    The Nile is located in Egypt.
    The Tiber is located in Italy.
    The Yangtze is located in China.
    
    Rivers: Nile
    
    Rivers: Tiber
    
    Rivers: Yangtze
    
    Country: Egypt
    
    Country: Italy
    
    Country: China



```python
# 6.7
person_1 = {'first_name': 'Abigail', 'last_name': 'Kojoian', 'age': '21', 'city': 'Cranston'}
person_2 = {'first_name':'Desiree', 'last_name': 'Manley', 'age': '58', 'city': 'Foster'}
person_3 = {'first_name': 'Lora', 'last_name': 'Calise', 'age': '55', 'city': 'Warwick'}

people = [person_1, person_2, person_3]

for person in people:
    print(person['first_name'])
    print(person['last_name'])
    print(person['age'])
    print(person['city'])
```

    Abigail
    Kojoian
    21
    Cranston
    Desiree
    Manley
    58
    Foster
    Lora
    Calise
    55
    Warwick



```python
# 6.8
pet_1 = {'kind': 'german_shepherd', 'owner_name': 'kevin'}
pet_2 = {'kind': 'labradour_retriever', 'owner_name': 'elena'}
pet_3 = {'kind': 'golden_doodle', 'owner_name': 'deirdre'}

pets = [pet_1, pet_2, pet_3]

for pet in pets:
    print(pet['kind'].title())
    print(pet['owner_name'].title())
```


      Input In [107]
        print(pet['kind'].title())\n
                                   ^
    SyntaxError: unexpected character after line continuation character




```python
# 6.9
favorite_places = {'Nathan': 'Paris', 'Ben': 'London', 'Sam': 'Milan'}

for key, value in favorite_places.items():
    print(f"\nKey: {key}")
    print(f"Value: {value}")
```

    
    Key: Nathan
    Value: Paris
    
    Key: Ben
    Value: London
    
    Key: Sam
    Value: Milan



```python
# 6.11

cities = {
    'Lisbon': { 
    'country': 'Portugal',
    'population':'2986000', 
    'fact': 'was not always the capital of Portugal'
    }, 
    'Rome': {
    'country': 'Italy', 
    'population': '4298000', 
    'fact': 'has more foundations than any other city on the planet'
    },
    'Yerevan': {
    'country': 'Armenia',
    'population': '1092000',
    'fact': 'Yerevan is 29 years older than Rome'
    },
}
```


```python
# 6.11 (cont.) 
for city, city_facts in cities.items():
    print(f"\nCity: {city}")
    net_info = f"{city_facts['country']}, {city_facts['population']}, {city_facts['fact']}"
    print(f"\tnet_info: {net_info.title()}")
```

    
    City: Lisbon
    	net_info: Portugal, 2986000, Was Not Always The Capital Of Portugal
    
    City: Rome
    	net_info: Italy, 4298000, Has More Foundations Than Any Other City On The Planet
    
    City: Yerevan
    	net_info: Armenia, 1092000, Yerevan Is 29 Years Older Than Rome

