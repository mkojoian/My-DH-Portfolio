---
layout: page
title: Exercises Chapter 8
description: Select exercises from Python Crash Course
---

```python
# 8.1
def display_message():
    """Display a message."""
    print("I am learning about functions in this chapter.")

display_message()
```

    I am learning about functions in this chapter.



```python
# 8.2
def favorite_book(title):
    """Displays title of favorite book."""
    print(f"One of my favorite books is {title.title()}.")

favorite_book('Great Expectations')
```

    One of my favorite books is Great Expectations.



```python
# 8.3

# Positional argument:

def make_shirt(shirt_size, shirt_message):
    """Display information about a t-shirt"""
    print(f"I have a {shirt_size} t-shirt.")
    print(f"My t-shirt says, '{shirt_message}.'")
make_shirt('small', 'I love Python')


# Keyword argument:
def make_shirt(shirt_size, shirt_message):
    """Display information about a t-shirt"""
    print(f"\nI have a {shirt_size} t-shirt.")
    print(f"My t-shirt says, '{shirt_message}.'")
make_shirt(shirt_size = 'small', shirt_message = 'I love Python.')
```

    I have a small t-shirt.
    My t-shirt says, 'I love Python.'
    
    I have a small t-shirt.
    My t-shirt says, 'I love Python..'



```python
# 8.5
def describe_city(city_name, country_name):
    """Display information about a city."""
    print(f"\n{city_name.title()} is in {country_name.title()}.") 
    
describe_city(city_name='naples', country_name='italy')
describe_city(city_name='rome', country_name='italy')
describe_city(city_name='providence', country_name='italy')

    
```

    
    Naples is in Italy.
    
    Rome is in Italy.
    
    Providence is in Italy.



```python
# 8.6
def get_city_country(city_name, country_name):
    """Return name of city, country."""
    city_country = f"{city_name} {country_name}"
    return city_country.title()

pair_A = get_city_country('"naples,', 'italy"')
print(pair_A)
    
pair_B = get_city_country('"rome,', 'italy"')
print(pair_B)   
    
pair_C = get_city_country('"providence,', 'italy"')
print(pair_C) 





```

    "Naples, Italy"
    "Rome, Italy"
    "Providence, Italy"



```python
# 8.7 part a.
def build_make_album(artist_name, album_title):
    """Return a dictionary of information about an album."""
    make_album = {'name': artist_name, 'title': album_title}
    return make_album

example_1 = build_make_album('Taylor Swift', 'Love Story')
print(example_1)

example_2 = build_make_album('Justin Bieber', 'Stay')
print(example_2)

example_3 = build_make_album('Ed Sheeran', 'Perfect')
print(example_3)

```

    {'name': 'Taylor Swift', 'title': 'Love Story'}
    {'name': 'Justin Bieber', 'title': 'Stay'}
    {'name': 'Ed Sheeran', 'title': 'Perfect'}



```python
# 8.7 part b.
def build_make_album(artist_name, album_title, number=None):
    """Return a dictionary of information about an album."""
    make_album = {'name': artist_name, 'title': album_title}
    if number:
        make_album['number'] = number 
    return make_album

example_1 = build_make_album('Taylor', 'Swift', number=7)
print(example_1)
example_2 = build_make_album('Justin', 'Bieber')
print(example_2)
example_3 = build_make_album('Ed', 'Sheeran', number=10)
print(example_3)
```

    {'name': 'Taylor', 'title': 'Swift', 'number': 7}
    {'name': 'Justin', 'title': 'Bieber'}
    {'name': 'Ed', 'title': 'Sheeran', 'number': 10}

