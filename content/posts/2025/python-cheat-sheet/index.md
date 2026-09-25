---
title: Python Cheat Sheet
summary: "Quick reference for core Python: data types, loops, functions, classes, exceptions, and more"
categories:
- Programming
tags:
  - python
date: 2025-08-21T10:28:49+02:00
draft: false
---

## Variables and Data Types
```python
string_var = "Hello, World!"
int_var = 42
float_var = 3.14
bool_var = True

list_var = [1, 2, 3, "four", 5.0]  # Mutable
tuple_var = (1, 2, 3)  # Immutable
dict_var = {"name": "Alice", "age": 30, "is_student": False}
set_var = {1, 2, 3, 4, 5}
```

## Type Casting
```python
str(int_var)  # Converts int to string
int(float_var)  # Converts float to int
float(int_var)  # Converts int to float
bool_var = bool(1)  # Converts integer to boolean (1 is True, 0 is False)
list_var = list(set_var)  # Converts set to list
tuple_var = tuple(list_var)  # Converts list to tuple
```

## User Input
```python
user_input = input("Enter something: ")
```

## Arithmetic Operations
```python
friends = 0
friends += 1  # Increment
friends -= 1  # Decrement
friends *= 2  # Multiply
friends /= 2  # Divide
friends %= 2  # Modulus
friends **= 2  # Exponentiation
friends //= 2  # Floor division

round_value = round(3.14159, 2)  # Rounds to 2 decimal places
abs_value = abs(-5)  # Absolute value
pow_value = pow(2, 3)  # 2 raised to the power of 3
max_value = max(1, 2, 3)  # Maximum value
min_value = min(1, 2, 3)  # Minimum value

import math
math.pi  # Pi constant
math.e  # Euler's number
math.sqrt(16)  # Square root
math.ceil(4.2)  # Ceiling value
math.floor(4.8)  # Floor value

# Example
circumference = 2 * math.pi * 5  # Circumference of a circle with radius 5
area = math.pi * pow(5, 2)  # Area of a circle with radius 5
```

## Comparison Operations
```python
if int_var > 10:
    print("int_var is greater than 10")
elif int_var < 10:
    print("int_var is less than 10")
else:
    print("int_var is equal to 10")
```

## Logical Operations
```python
if bool_var and (int_var > 20):
    print("Both conditions are True")
elif bool_var or (int_var < 20):
    print("At least one condition is True")
else:
    print("Both conditions are False")
```

## Conditional Statements
```python
if int_var > 10:
    print("int_var is greater than 10")
elif int_var < 10:
    print("int_var is less than 10")
else:
    print("int_var is equal to 10")
```

## String Methods
```python
"Hello, World!".find("World")  # Returns the index of "World" in the string
"Hello, World!".rfind("World")  # Returns the index of "World" in the string, searching from the end
"Hello, World!".capitalize()  # Capitalizes the first letter of the string
"Hello, World!".upper()  # Converts the string to uppercase
"Hello, World!".lower()  # Converts the string to lowercase
"Hello, World!".isdigit()  # Checks if the string contains only digits
"Hello, World!".isalpha()  # Checks if the string contains only alphabetic characters
"Hello, World!".count("o")  # Counts occurrences of "o" in the string
"Hello, World!".replace("World", "Python")  # Replaces "World" with "Python"
```

## String Indexing
```python
string_var = "Hello, World!"
first_char = string_var[0]  # 'H'
last_char = string_var[-1]  # '!'
substring = string_var[0:5]  # 'Hello'
substring_from_end = string_var[-6:]  # 'World!'
reversed_string = string_var[::-1]  # '!dlroW ,olleH'
```

## Format Specifiers
```python
price1 = 3.14159
price2 = -987.65
price3 = 12.34

f"Price 1: {price1:.2f}, Price 2: {price2:.2f}, Price 3: {price3:.2f}"
f"Price 1: {price1:10}, Price 2: {price2:10}, Price 3: {price3:10}"
f"Price 1: {price1:<10}, Price 2: {price2:<10}, Price 3: {price3:<10}"  # Left aligned
f"Price 1: {price1:>10}, Price 2: {price2:>10}, Price 3: {price3:>10}"  # Right aligned
f"Price 1: {price1:^10}, Price 2: {price2:^10}, Price 3: {price3:^10}"  # Centered
f"Price 1: {price1:+}, Price 2: {price2:+}, Price 3: {price3:+}"  # Show sign
f"Price 1: {price1: }, Price 2: {price2: }, Price 3: {price3: }"  # Space for sign
f"Price 1: {price1:,}, Price 2: {price2:,}, Price 3: {price3:,}"  # Thousands separator
```

## While Loops
```python
name = input("Enter your name: ")
while name == "":
    print("Name cannot be empty. Please try again.")
    name = input("Enter your name: ")

# Better
while True:
    name = input("Enter your name: ")
    if name:
        break
    print("Name cannot be empty. Please try again.")
```

## For Loops
```python
for i in range(5):
    print(i)  # Prints numbers from 0 to 4

for i in reversed(range(5)):
    print(i)  # Prints numbers from 4 to 0

for i in range(1, 10, 2):
    print(i)  # Prints odd numbers from 1 to 9

for char in "Hello":
    print(char)  # Prints each character in the string "Hello"
```

## Nested Loops
```python
for i in range(3):
    for j in range(2):
        print(f"i: {i}, j: {j}")  # Prints combinations of i and j
```

## Collections
```python
# List
my_list = [1, 2, 3, 4, 5]
my_list.append(6)  # Adds 6 to the end of the list
my_list.remove(3)  # Removes the first occurrence of 3 from the list
my_list.insert(2, 2.5)  # Inserts 2.5 at index 2
my_list.sort()  # Sorts the list in ascending order
my_list.reverse()  # Reverses the order of the list
my_list.index(4)  # Returns the index of the first occurrence of 4
my_list.count(2)  # Returns the number of occurrences of 2 in the list
my_list.clear()  # Removes all elements from the list

# Tuple
my_tuple = (1, 2, 3, 4, 5)  # Tuples are immutable, so you cannot modify them after creation

# Set
my_set = {1, 2, 3, 4, 5}
my_set.add(6)  # Adds 6 to the set
my_set.remove(3)  # Removes 3 from the set
my_set.union({7, 8})  # Combines two sets
my_set.intersection({2, 3, 4})  # Finds common elements in two sets
my_set.difference({2, 3})  # Finds elements in the first set that are not in the second set

# Dictionary
my_dict = {"name": "Alice", "age": 30, "is_student": False}
my_dict["city"] = "New York"  # Adds a new key-value pair
my_dict["age"] = 31  # Updates the value for the key "age"
my_dict.update({"country": "USA"})  # Updates the dictionary with new key-value pairs
my_dict.pop("is_student")  # Removes the key "is_student" and its value
my_dict.popitem()  # Removes and returns the last inserted key-value pair
my_dict.keys()  # Returns a view of the keys in the dictionary
my_dict.values()  # Returns a view of the values in the dictionary
my_dict.items()  # Returns a view of the key-value pairs in the dictionary
my_dict.get("name")  # Returns the value for the key "name"
my_dict.get("nonexistent_key", "default_value")  # Returns "default_value" if the key does not exist
```

## Random
```python
import random
random.randint(1, 10)  # Returns a random integer between 1 and 10 (inclusive)
random.choice([1, 2, 3, 4, 5])  # Returns a random element from the list
random.shuffle([1, 2, 3, 4, 5])  # Shuffles the list in place
random.sample([1, 2, 3, 4, 5], 3)  # Returns a list of 3 unique random elements from the list
random.uniform(1.0, 10.0)  # Returns a random float between 1.0 and 10.0
random.seed(42)  # Sets the seed for reproducibility
random.random()  # Returns a random float between 0.0 and 1.0
```

## Functions
```python
def greet(name):
    """Prints a greeting message."""
    print(f"Hello, {name}!")

greet("Alice")
```

### Return Values
```python
def add(a, b):
    """Returns the sum of a and b."""
    return a + b
```

### Default Arguments
```python
def greet(name="World"):
    """Prints a greeting message with a default name."""
    print(f"Hello, {name}!")
```

### Keyword Arguments
```python
def greet(name, greeting="Hello"):
    """Prints a custom greeting message."""
    print(f"{greeting}, {name}!")

greet("Alice", greeting="Hi")
```

### Arbitrary Arguments
```python
def add(*args):
    return sum(args)

def print_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")
```

## List Comprehensions
```python
squares = [x**2 for x in range(10)]  # List of squares from 0 to 9
evens = [x for x in range(10) if x % 2 == 0]  # List of even numbers from 0 to 9
squares_and_evens = [(x, x**2) for x in range(10) if x % 2 == 0]  # List of tuples (number, square) for even numbers
```

## Match-Case Statement (switch)
```python
def describe_number(num):
    match num:
        case 0:
            return "Zero"
        case 1:
            return "One"
        case 2 | 3:
            return "Two or Three"
        case _ if num < 0:
            return "Negative number"
        case _:
            return "Some other number"
```

## Classes
```python
class Dog:
    dogs_count = 0

    def __init__(self, name):
        self._name = name
        Dog.dogs_count += 1

    def bark(self):
        print(f"{self._name} says Woof!")

    @classmethod
    def get_dogs_count(cls):
        """Class method can access class variables."""
        return cls.dogs_count

    @staticmethod
    def is_domestic():
        """Static method does not access class or instance variables."""
        return True
    
    @property
    def name(self):
        return self._name
        
    @name.setter
    def name(self, new_name):
        self._name = new_name
        
    @name.deleter
    def name(self):
        print(f"Deleting name {self._name}")
        del self._name
        
my_dog = Dog("Buddy")

my_dog.bark()  # Outputs: Buddy says Woof!
print(Dog.get_dogs_count())  # Outputs: 1
print(Dog.is_domestic())  # Outputs: True
print(my_dog.name)  # Outputs: Buddy

my_dog1 = Dog("Max")
print(Dog.dogs_count)  # Outputs: 2
```

## Inheritance
```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        raise NotImplementedError("Subclasses must implement this method")

class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"

my_dog = Dog("Buddy")
my_cat = Cat("Whiskers")
print(my_dog.speak())  # Outputs: Buddy says Woof!
print(my_cat.speak())  # Outputs: Whiskers says Meow!
```

## Multiple Inheritance
```python
class Flyer:
    def fly(self):
        return "Flying"

class Swimmer:
    def swim(self):
        return "Swimming"

class Duck(Flyer, Swimmer):
    def quack(self):
        return "Quack!"

my_duck = Duck()
print(my_duck.fly())  # Outputs: Flying
print(my_duck.swim())  # Outputs: Swimming
print(my_duck.quack())  # Outputs: Quack!
```

## Super
```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return f"{self.name} makes a sound"

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)  # Call the constructor of the parent class
        self.breed = breed
        
    def speak(self):
        return f"{self.name} the {self.breed} says Woof!"

my_dog = Dog("Buddy", "Golden Retriever")
print(my_dog.speak())  # Outputs: Buddy the Golden Retriever says Woof!
print(my_dog.name)  # Outputs: Buddy
print(my_dog.breed)  # Outputs: Golden Retriever
```

## Polymorphism
```python
from abc import ABC, abstractmethod

class Bird(ABC):
    @abstractmethod
    def fly(self):
        pass
        
class Penguin(Bird):
    def fly(self):
        return "Cannot fly, but swims"

class Sparrow(Bird):
    def fly(self):
        return "Flies gracefully"

def make_bird_fly(bird):
    print(bird.fly())

my_penguin = Penguin()
my_sparrow = Sparrow()
make_bird_fly(my_penguin)  # Outputs: Cannot fly, but swims
make_bird_fly(my_sparrow)  # Outputs: Flies gracefully
```

## Magic Methods
```python
class Score:
    def __init__(self, points=0):
        self.points = points

    def __add__(self, other):
        return Score(self.points + other.points)

    def __sub__(self, other):
        return Score(self.points - other.points)

    def __mul__(self, number):
        return Score(self.points * number)

    def __str__(self):
        return f"{self.points}"
        
    def __eq__(self, other):
        return self.points == other.points
        
    def __lt__(self, other):
        return self.points < other.points
        
    def __gt__(self, other):
        return self.points > other.points
        
    def __contains__(self, item):
        return item <= self.points
        
    def __len__(self):
        return self.points
        
    def __getitem__(self, index):
        points_str = str(self.points)
        return int(points_str[index]) if index < len(points_str) else None
```

## Decorators
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Something is happening before the function is called.")
        result = func(*args, **kwargs)
        print("Something is happening after the function is called.")
        return result

    return wrapper

@my_decorator
def say_hello():
    print("Hello!")
```

## Exceptions
```python
try:
    number = int(input("Enter a number: "))
    result = 10 / number
except ValueError:
    print("Invalid input. Please enter a valid integer.")
except ZeroDivisionError:
    print("Cannot divide by zero.")
else:
    print(f"Result is {result}")
finally:
    print("Execution completed.")
```

## File Handling
```python
# Writing to a file
with open("example.txt", "w") as file:
    file.write("Hello, World!\n")

# Reading from a file
with open("example.txt", "r") as file:
    content = file.read()
    print(content)

# Appending to a file
with open("example.txt", "a") as file:
    file.write("Appending a new line.\n")
```

## Date and Time
```python
import datetime

now = datetime.datetime.now()  # Current date and time
today = datetime.date.today()  # Current date
time = datetime.datetime.now().time()  # Current time
formatted_date = now.strftime("%Y-%m-%d %H:%M:%S")  # Format date and time
```

## Multithreading
```python
import threading

def print_numbers(number):
    for i in range(number):
        print(i)

def print_letters():
    for letter in 'abcde':
        print(letter)

thread1 = threading.Thread(target=print_numbers, args=(5,))
thread2 = threading.Thread(target=print_letters)
thread1.start()
thread2.start()
thread1.join()
thread2.join()
```

## Requests
```python
import requests

response = requests.get("https://api.github.com")
if response.status_code == 200:
    print("Success!")
    data = response.json()
    print(data)
else:
    print("Failed to retrieve data:", response.status_code)
```
