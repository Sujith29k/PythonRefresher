# Python Fundamentals: Complete Revision Guide

## Table of Contents

1. [Python Syntax and Execution Model](#python-syntax-and-execution-model)
2. Data Types and Variables
3. Control Flow (if, loops, match)
4. Functions and Functional Concepts
5. Object-Oriented Programming
6. Error and Exception Handling
7. Modules, Packages, and Virtual Environments
8. Standard Library Essentials
9. Memory Management and References
10. Iterators, Generators, and Decorators
11. Basic Concurrency (Threading, Multiprocessing, Async)
12. Interview Preparation

---

## Python Syntax and Execution Model

### Overview

Python is an interpreted, dynamically-typed, high-level programming language. Understanding how Python executes code and its syntax rules is fundamental to writing effective Python programs.

### Python Execution Flow

Python code goes through several stages before execution:

1. **Source Code (.py)** → Written by developers
2. **Bytecode (.pyc)** → Compiled by Python interpreter
3. **Python Virtual Machine (PVM)** → Executes bytecode

```mermaid
flowchart LR
    A[Source Code<br/>.py file] --> B[Python Compiler]
    B --> C[Bytecode<br/>.pyc file]
    C --> D[Python Virtual Machine]
    D --> E[Execution]

    style A fill:#e1f5ff
    style C fill:#fff4e1
    style E fill:#e8f5e9
```

### Key Execution Concepts

#### 1. Interpretation vs Compilation

Python is **interpreted**, but it's not purely interpreted. The source code is first compiled to bytecode, which is then interpreted by the PVM.

```python
# When you run this file, Python:
# 1. Compiles it to bytecode
# 2. Stores bytecode in __pycache__ (for imports)
# 3. Executes bytecode in PVM

def greet(name):
    return f"Hello, {name}!"

print(greet("Python"))
```

#### 2. The Python Execution Model

```python
# Python executes code top-to-bottom, line-by-line
print("First")   # Executes first
x = 10          # Then this
print("Second")  # Then this

# Function definitions are executed (registered), but body runs only when called
def calculate():  # This line executes (function object created)
    return 5 + 5  # This line does NOT execute until function is called

result = calculate()  # NOW the function body executes
```

#### 3. The Global Interpreter Lock (GIL)

Python has a GIL that allows only one thread to execute Python bytecode at a time, even on multi-core systems.

```python
import threading

# Due to GIL, these threads won't run truly in parallel for CPU-bound tasks
def cpu_intensive_task():
    total = sum(i * i for i in range(10_000_000))
    return total

# Two threads, but only one executes Python bytecode at a time
t1 = threading.Thread(target=cpu_intensive_task)
t2 = threading.Thread(target=cpu_intensive_task)
```

```mermaid
sequenceDiagram
    participant T1 as Thread 1
    participant GIL as Global Interpreter Lock
    participant T2 as Thread 2

    T1->>GIL: Acquire GIL
    GIL-->>T1: Lock granted
    T1->>T1: Execute bytecode
    T1->>GIL: Release GIL
    GIL-->>T2: Lock granted
    T2->>T2: Execute bytecode
    T2->>GIL: Release GIL
```

### Python Syntax Fundamentals

#### 1. Indentation is Semantic

Python uses indentation to define code blocks (not braces like C/Java).

```python
# Correct
if True:
    print("Indented")
    print("Part of if block")

# SyntaxError: inconsistent indentation
if True:
    print("4 spaces")
      print("6 spaces - ERROR!")

# Best practice: Use 4 spaces (PEP 8 standard)
def function():
    if condition:
        for item in items:
            print(item)  # Consistent 4-space indentation
```

#### 2. Dynamic Typing

Variables don't have fixed types; the type is determined at runtime.

```python
# Same variable can hold different types
x = 10           # x is int
x = "Hello"      # Now x is str
x = [1, 2, 3]   # Now x is list

# Type checking happens at runtime
def add(a, b):
    return a + b

add(5, 3)        # Works: 8
add("Hi", "!")   # Works: "Hi!"
add(5, "Hi")     # TypeError at runtime
```

#### 3. Everything is an Object

In Python, everything (including functions, classes, modules) is an object.

```python
# Numbers are objects
x = 42
print(type(x))  # <class 'int'>
print(x.__class__)  # <class 'int'>

# Functions are objects
def greet():
    return "Hello"

print(type(greet))  # <class 'function'>
another_name = greet  # Assign function to another variable
print(another_name())  # "Hello"

# Classes are objects
class MyClass:
    pass

print(type(MyClass))  # <class 'type'>
```

```mermaid
classDiagram
    class object {
        <<base>>
    }
    class type {
        <<metaclass>>
    }
    class int
    class str
    class function
    class MyClass

    object <|-- type
    object <|-- int
    object <|-- str
    object <|-- function
    object <|-- MyClass
    type <|-- MyClass : instance of
```

#### 4. Name Binding and Scopes

Python uses name binding rather than traditional variable assignment.

```python
# Name binding
x = 10  # Name 'x' is bound to object 10

# Multiple names can bind to same object
y = x   # 'y' is bound to same object as 'x'

# Rebinding
x = 20  # 'x' now bound to different object (y still points to 10)

print(x)  # 20
print(y)  # 10
```

**LEGB Rule** - Python searches for names in this order:

- **L**ocal: Inside current function
- **E**nclosing: In any enclosing functions
- **G**lobal: Module level
- **B**uilt-in: Python built-ins

```python
x = "global"  # Global scope

def outer():
    x = "enclosing"  # Enclosing scope

    def inner():
        x = "local"  # Local scope
        print(x)  # Prints "local"

    inner()
    print(x)  # Prints "enclosing"

outer()
print(x)  # Prints "global"

# Built-in scope
print(len([1, 2, 3]))  # 'len' from built-in scope
```

```mermaid
flowchart TD
    A[Variable Lookup] --> B{Local Scope?}
    B -->|Found| Z[Return Value]
    B -->|Not Found| C{Enclosing Scope?}
    C -->|Found| Z
    C -->|Not Found| D{Global Scope?}
    D -->|Found| Z
    D -->|Not Found| E{Built-in Scope?}
    E -->|Found| Z
    E -->|Not Found| F[NameError]

    style Z fill:#90EE90
    style F fill:#FFB6C6
```

#### 5. Interactive Mode vs Script Mode

```python
# Interactive mode (REPL)
# Every expression's value is automatically printed
>>> 5 + 5
10
>>> "hello"
'hello'

# Script mode (.py file)
# Expressions are evaluated but not automatically printed
5 + 5        # Result is calculated but not displayed
"hello"      # String is created but not displayed
print(5 + 5) # Explicitly print: 10
```

#### 6. Statements vs Expressions

- **Expression**: Evaluates to a value
- **Statement**: Performs an action

```python
# Expressions (return values)
5 + 3                    # Expression: evaluates to 8
"hello".upper()          # Expression: evaluates to "HELLO"
x if x > 0 else 0       # Conditional expression

# Statements (perform actions)
x = 10                   # Assignment statement
if x > 5:               # If statement
    print(x)            # Print statement
for i in range(5):      # For statement
    pass

# Some can be both
y = (x := 10)           # Assignment expression (walrus operator, Python 3.8+)
```

### Special Syntax Features

#### 1. Multiple Assignment

```python
# Tuple unpacking
x, y, z = 1, 2, 3

# Swap variables
a, b = b, a

# Extended unpacking (Python 3+)
first, *middle, last = [1, 2, 3, 4, 5]
# first = 1, middle = [2, 3, 4], last = 5
```

#### 2. Line Continuation

```python
# Implicit continuation (inside brackets)
result = (1 + 2 + 3 +
          4 + 5 + 6)

# Explicit continuation (backslash)
total = 1 + 2 + 3 + \
        4 + 5 + 6

# Better: Use implicit (PEP 8 preferred)
data = {
    'name': 'John',
    'age': 30,
    'city': 'NYC'
}
```

#### 3. Comments and Docstrings

```python
# Single-line comment

"""
Multi-line comment
(technically a string literal that's not assigned)
"""

def function(param):
    """
    Docstring: Describes function purpose, parameters, and return value.
    This is accessed via function.__doc__

    Args:
        param: Description of parameter

    Returns:
        Description of return value
    """
    return param * 2

# Accessing docstring
print(function.__doc__)
```

### Practical Example: Understanding Execution

```python
# This demonstrates Python's execution model

print("1. Module starts loading")

def outer_function():
    """This function is defined but not executed yet"""
    print("3. Outer function executes")

    def inner_function():
        """Nested function - defined when outer_function runs"""
        print("4. Inner function executes")

    inner_function()  # Call inner function

print("2. About to call outer_function")
outer_function()  # Now outer_function executes
print("5. Module finishes")

# Output order:
# 1. Module starts loading
# 2. About to call outer_function
# 3. Outer function executes
# 4. Inner function executes
# 5. Module finishes
```

### Interview Insight: Why This Matters

Understanding Python's execution model helps you:

- Debug subtle timing and scope issues
- Optimize performance (understanding GIL for concurrency)
- Write cleaner, more Pythonic code
- Answer questions about Python's internal workings

Common interview questions from this section:

- "Explain Python's GIL and its implications"
- "What is the difference between compiled and interpreted languages? Where does Python fit?"
- "Explain the LEGB rule with an example"
- "Why does Python use indentation instead of braces?"

---

## Data Types and Variables

### Overview

Python has several built-in data types that form the foundation of every program. Understanding these types, their behaviors, and how Python manages them is crucial for writing efficient code.

### Python's Type System

Python is **dynamically typed** (type checking at runtime) and **strongly typed** (no implicit type coercion in most cases).

```python
# Dynamic typing: type determined at runtime
x = 10        # int
x = "hello"   # now str - no error

# Strong typing: no automatic conversion
result = "5" + 5  # TypeError: can't concatenate str and int
result = "5" + str(5)  # "55" - explicit conversion required
```

### Built-in Data Types

Python's built-in types are categorized as:

```mermaid
graph TD
    A[Python Data Types] --> B[Numeric]
    A --> C[Sequence]
    A --> D[Mapping]
    A --> E[Set]
    A --> F[Boolean]
    A --> G[Binary]
    A --> H[None]

    B --> B1[int]
    B --> B2[float]
    B --> B3[complex]

    C --> C1[str]
    C --> C2[list]
    C --> C3[tuple]
    C --> C4[range]

    D --> D1[dict]

    E --> E1[set]
    E --> E2[frozenset]

    G --> G1[bytes]
    G --> G2[bytearray]
    G --> G3[memoryview]

    style A fill:#FFE6E6
    style B fill:#E6F3FF
    style C fill:#E6FFE6
    style D fill:#FFF9E6
    style E fill:#F0E6FF
```

### 1. Numeric Types

#### Integer (int)

Arbitrary precision integers - no overflow!

```python
# Python 3: int has unlimited precision
big_number = 10 ** 100  # 1 followed by 100 zeros - no problem!
print(big_number)

# Different bases
decimal = 42
binary = 0b101010      # Binary (base 2)
octal = 0o52           # Octal (base 8)
hexadecimal = 0x2A     # Hexadecimal (base 16)

print(decimal == binary == octal == hexadecimal)  # True

# Conversions
print(bin(42))   # '0b101010'
print(oct(42))   # '0o52'
print(hex(42))   # '0x2a'

# Integer division vs floor division
print(7 / 2)     # 3.5 (float division)
print(7 // 2)    # 3 (floor division - returns int)
print(-7 // 2)   # -4 (floors toward negative infinity)
```

#### Float (float)

Double-precision floating-point numbers (64-bit).

```python
# Float representation
x = 3.14
y = 2.5e-3  # Scientific notation: 0.0025
z = float('inf')   # Positive infinity
w = float('-inf')  # Negative infinity
nan = float('nan') # Not a Number

# Precision issues (important!)
print(0.1 + 0.2)  # 0.30000000000000004 (not exactly 0.3!)

# Use decimal for precision-critical applications
from decimal import Decimal
a = Decimal('0.1')
b = Decimal('0.2')
print(a + b)  # Decimal('0.3') - exact!

# Checking special values
import math
print(math.isinf(z))    # True
print(math.isnan(nan))  # True

# Float operations
print(5 / 2)      # 2.5
print(5 % 2)      # 1 (modulo)
print(5 ** 2)     # 25 (exponentiation)
print(pow(5, 2))  # 25 (same as **)
```

#### Complex (complex)

Numbers with real and imaginary parts.

```python
# Complex numbers
z1 = 3 + 4j  # or complex(3, 4)
z2 = 2 - 1j

print(z1.real)  # 3.0
print(z1.imag)  # 4.0

# Operations
print(z1 + z2)       # (5+3j)
print(z1 * z2)       # (10+5j)
print(abs(z1))       # 5.0 (magnitude)
print(z1.conjugate()) # (3-4j)
```

### 2. Sequence Types

#### String (str)

Immutable sequences of Unicode characters.

```python
# String creation
s1 = 'single quotes'
s2 = "double quotes"
s3 = '''triple quotes
can span multiple
lines'''
s4 = """also with double"""

# Raw strings (escape sequences ignored)
path = r"C:\new\folder"  # Backslashes are literal

# F-strings (formatted string literals, Python 3.6+)
name = "Alice"
age = 30
message = f"My name is {name} and I'm {age} years old"
print(message)

# Expression in f-strings
print(f"Next year I'll be {age + 1}")
print(f"Uppercase: {name.upper()}")

# String operations
text = "Python Programming"
print(len(text))          # 18
print(text[0])            # 'P' (indexing)
print(text[-1])           # 'g' (negative indexing from end)
print(text[0:6])          # 'Python' (slicing)
print(text[::2])          # 'Pto rgamn' (step slicing)
print(text[::-1])         # 'gnimmargorP nohtyP' (reverse)

# Immutability
# text[0] = 'J'  # TypeError: str object does not support item assignment

# String methods (returns new string)
print("hello".upper())           # 'HELLO'
print("HELLO".lower())           # 'hello'
print("  spaces  ".strip())      # 'spaces'
print("a,b,c".split(","))        # ['a', 'b', 'c']
print(",".join(['a', 'b', 'c'])) # 'a,b,c'
print("hello".replace('l', 'L')) # 'heLLo'
print("hello".startswith('he'))  # True
print("123".isdigit())           # True

# String formatting (multiple ways)
name, age = "Bob", 25
print("Name: %s, Age: %d" % (name, age))  # Old style
print("Name: {}, Age: {}".format(name, age))  # .format()
print(f"Name: {name}, Age: {age}")  # f-string (preferred)
```

#### List (list)

Mutable, ordered sequences - can contain mixed types.

```python
# List creation
empty = []
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True, [1, 2]]  # Can mix types

# List operations
print(len(numbers))     # 5
print(numbers[0])       # 1
print(numbers[-1])      # 5
print(numbers[1:4])     # [2, 3, 4]

# Mutability - lists can be modified
numbers[0] = 10
print(numbers)  # [10, 2, 3, 4, 5]

# List methods
numbers.append(6)           # Add to end: [10, 2, 3, 4, 5, 6]
numbers.insert(0, 0)        # Insert at position: [0, 10, 2, 3, 4, 5, 6]
numbers.extend([7, 8])      # Add multiple: [0, 10, 2, 3, 4, 5, 6, 7, 8]
removed = numbers.pop()     # Remove and return last: 8
numbers.remove(10)          # Remove first occurrence of 10
print(numbers.index(5))     # Get index of 5
print(numbers.count(2))     # Count occurrences of 2
numbers.reverse()           # Reverse in place
numbers.sort()              # Sort in place

# List comprehension (powerful!)
squares = [x**2 for x in range(10)]  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
evens = [x for x in range(10) if x % 2 == 0]  # [0, 2, 4, 6, 8]

# Nested list comprehension
matrix = [[i*j for j in range(3)] for i in range(3)]
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]

# List unpacking
first, *rest, last = [1, 2, 3, 4, 5]
# first=1, rest=[2, 3, 4], last=5
```

#### Tuple (tuple)

Immutable, ordered sequences - often used for heterogeneous data.

```python
# Tuple creation
empty = ()
single = (1,)  # Note the comma! (1) is just 1, not a tuple
coordinates = (10, 20)
mixed = (1, "hello", 3.14)

# Tuple packing and unpacking
point = 10, 20, 30  # Packing (parentheses optional)
x, y, z = point     # Unpacking

# Immutability
# coordinates[0] = 15  # TypeError: tuple object does not support item assignment

# But if tuple contains mutable objects...
t = ([1, 2], [3, 4])
t[0].append(3)  # This works! The list inside is mutable
print(t)  # ([1, 2, 3], [3, 4])

# Tuple methods (only 2!)
numbers = (1, 2, 2, 3, 2)
print(numbers.count(2))  # 3
print(numbers.index(3))  # 3

# Named tuples (from collections)
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
print(p.x, p.y)  # 10 20
print(p[0], p[1])  # 10 20 (still works like regular tuple)

# When to use tuple vs list?
# Tuple: Heterogeneous data (like a record), immutable data, dict keys
# List: Homogeneous data (like a collection), data that changes
```

#### Range (range)

Immutable sequence of numbers - memory efficient!

```python
# Range creation
r1 = range(5)        # 0 to 4
r2 = range(1, 10)    # 1 to 9
r3 = range(0, 10, 2) # 0, 2, 4, 6, 8 (step=2)

# Range doesn't store all values - generates them on demand
r = range(1000000)  # Creates instantly, uses minimal memory

# Convert to list to see values
print(list(range(5)))  # [0, 1, 2, 3, 4]

# Common use: for loops
for i in range(5):
    print(i)

# Reverse range
for i in range(10, 0, -1):
    print(i)  # 10, 9, 8, ..., 1
```

### 3. Mapping Type

#### Dictionary (dict)

Mutable, unordered (Python 3.7+ maintains insertion order) key-value pairs.

```python
# Dictionary creation
empty = {}
person = {
    'name': 'Alice',
    'age': 30,
    'city': 'NYC'
}

# Alternative: dict() constructor
person2 = dict(name='Bob', age=25, city='LA')

# Accessing values
print(person['name'])  # 'Alice'
print(person.get('age'))  # 30
print(person.get('country', 'USA'))  # 'USA' (default if key not found)

# Mutability
person['age'] = 31  # Modify
person['country'] = 'USA'  # Add new key-value pair
del person['city']  # Delete key

# Dictionary methods
print(person.keys())    # dict_keys(['name', 'age', 'country'])
print(person.values())  # dict_values(['Alice', 31, 'USA'])
print(person.items())   # dict_items([('name', 'Alice'), ('age', 31), ('country', 'USA')])

# Checking membership
print('name' in person)  # True (checks keys, not values)

# Dictionary comprehension
squares = {x: x**2 for x in range(5)}  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Merging dictionaries (Python 3.9+)
dict1 = {'a': 1, 'b': 2}
dict2 = {'c': 3, 'd': 4}
merged = dict1 | dict2  # {'a': 1, 'b': 2, 'c': 3, 'd': 4}

# Update in place
dict1.update(dict2)

# Dictionary unpacking
def greet(name, age):
    print(f"{name} is {age}")

info = {'name': 'Charlie', 'age': 28}
greet(**info)  # Unpacks dict as keyword arguments

# Nested dictionaries
users = {
    'user1': {'name': 'Alice', 'age': 30},
    'user2': {'name': 'Bob', 'age': 25}
}
print(users['user1']['name'])  # 'Alice'

# setdefault and defaultdict
count = {}
for char in "hello":
    count[char] = count.get(char, 0) + 1
# Or use setdefault
count = {}
for char in "hello":
    count.setdefault(char, 0)
    count[char] += 1

# defaultdict (from collections)
from collections import defaultdict
count = defaultdict(int)  # Default value is 0
for char in "hello":
    count[char] += 1  # No need to check if key exists!
```

### 4. Set Types

#### Set (set)

Mutable, unordered collection of unique elements.

```python
# Set creation
empty = set()  # Not {}! That's an empty dict
numbers = {1, 2, 3, 4, 5}
mixed = {1, "hello", 3.14, True}

# Note: True and 1 are considered equal in sets
s = {1, True, 2, False, 0}
print(s)  # {False, 1, 2} (duplicates removed)

# Set operations
numbers.add(6)       # Add element
numbers.remove(1)    # Remove (raises error if not found)
numbers.discard(10)  # Remove (no error if not found)
print(2 in numbers)  # True (membership test - O(1) average!)

# Mathematical set operations
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}

print(a | b)  # Union: {1, 2, 3, 4, 5, 6, 7, 8}
print(a & b)  # Intersection: {4, 5}
print(a - b)  # Difference: {1, 2, 3}
print(a ^ b)  # Symmetric difference: {1, 2, 3, 6, 7, 8}

# Or use methods
print(a.union(b))
print(a.intersection(b))
print(a.difference(b))
print(a.symmetric_difference(b))

# Set comprehension
squares = {x**2 for x in range(10)}

# Remove duplicates from list
numbers = [1, 2, 2, 3, 3, 3, 4]
unique = list(set(numbers))  # [1, 2, 3, 4] (order not preserved)
```

#### Frozenset (frozenset)

Immutable version of set - can be used as dictionary keys.

```python
# Frozenset creation
fs = frozenset([1, 2, 3, 4, 5])

# Immutable - no add, remove, etc.
# fs.add(6)  # AttributeError

# Can be used as dict keys (sets can't)
lookup = {
    frozenset([1, 2]): 'pair one',
    frozenset([3, 4]): 'pair two'
}

# Set operations still work
a = frozenset([1, 2, 3])
b = frozenset([3, 4, 5])
print(a | b)  # frozenset({1, 2, 3, 4, 5})
```

### 5. Boolean Type

```python
# Boolean values
is_valid = True
is_empty = False

# Boolean operations
print(True and False)  # False
print(True or False)   # True
print(not True)        # False

# Truthiness - every object has a boolean value
# Falsy values: False, None, 0, 0.0, '', [], {}, set()
# Everything else is truthy

if []:
    print("Empty list is truthy")  # Won't print
else:
    print("Empty list is falsy")   # Prints

# Comparison operators return booleans
print(5 > 3)   # True
print(5 == 5)  # True
print(5 != 3)  # True

# Chaining comparisons
x = 10
print(0 < x < 20)  # True (equivalent to: 0 < x and x < 20)

# Boolean conversion
print(bool(0))      # False
print(bool(42))     # True
print(bool(""))     # False
print(bool("text")) # True

# Short-circuit evaluation
def expensive():
    print("Called!")
    return True

result = False and expensive()  # expensive() never called!
result = True or expensive()    # expensive() never called!
```

### 6. None Type

```python
# None - Python's null/nil equivalent
x = None

# Only one None object exists
print(x is None)  # True (use 'is', not '==')

# None is falsy
if not None:
    print("None is falsy")  # Prints

# Common use: default parameter
def greet(name=None):
    if name is None:
        name = "Guest"
    print(f"Hello, {name}")

greet()        # "Hello, Guest"
greet("Alice") # "Hello, Alice"

# Functions without explicit return return None
def no_return():
    pass

result = no_return()
print(result)  # None
```

### Type Conversion (Type Casting)

```python
# Explicit conversions
print(int("42"))        # 42
print(int(3.9))         # 3 (truncates, doesn't round)
print(int("1010", 2))   # 10 (binary to int)

print(float("3.14"))    # 3.14
print(float(42))        # 42.0

print(str(42))          # "42"
print(str([1, 2, 3]))   # "[1, 2, 3]"

print(list("hello"))    # ['h', 'e', 'l', 'l', 'o']
print(list((1, 2, 3)))  # [1, 2, 3]

print(tuple([1, 2, 3])) # (1, 2, 3)

print(set([1, 2, 2, 3])) # {1, 2, 3}

# Dict from key-value pairs
print(dict([('a', 1), ('b', 2)]))  # {'a': 1, 'b': 2}

# Error handling
try:
    int("hello")  # ValueError
except ValueError as e:
    print(f"Conversion error: {e}")
```

### Type Checking

```python
# type() - returns the type
x = 42
print(type(x))  # <class 'int'>
print(type(x) == int)  # True

# isinstance() - preferred way to check type
print(isinstance(x, int))  # True
print(isinstance(x, (int, float)))  # True (checks multiple types)

# isinstance respects inheritance
class Animal:
    pass

class Dog(Animal):
    pass

d = Dog()
print(isinstance(d, Dog))     # True
print(isinstance(d, Animal))  # True (Dog inherits from Animal)
print(type(d) == Animal)      # False (type() doesn't respect inheritance)
```

### Memory Efficiency Comparison

```mermaid
graph LR
    A[Data Structure] --> B[Memory Usage]

    C[list] --> C1["High<br/>Stores all elements"]
    D[tuple] --> D1["Medium<br/>Less overhead than list"]
    E[set] --> E1["Medium-High<br/>Hash table overhead"]
    F[dict] --> F1["High<br/>Keys + values + hash table"]
    G[range] --> G1["Very Low<br/>Stores only start, stop, step"]

    style G1 fill:#90EE90
    style C1 fill:#FFB6C6
```

### Practical Example: Data Type Selection

```python
# Problem: Count word frequency in text
text = "apple banana apple cherry banana apple"

# Solution 1: Using dict
word_count = {}
for word in text.split():
    word_count[word] = word_count.get(word, 0) + 1
print(word_count)  # {'apple': 3, 'banana': 2, 'cherry': 1}

# Solution 2: Using defaultdict
from collections import defaultdict
word_count = defaultdict(int)
for word in text.split():
    word_count[word] += 1

# Solution 3: Using Counter (best!)
from collections import Counter
word_count = Counter(text.split())
print(word_count.most_common(2))  # [('apple', 3), ('banana', 2)]

# Problem: Remove duplicates while preserving order
items = [1, 2, 2, 3, 1, 4, 3, 5]

# Solution: dict.fromkeys (preserves order in Python 3.7+)
unique = list(dict.fromkeys(items))
print(unique)  # [1, 2, 3, 4, 5]

# Problem: Fast membership testing
# Use set (O(1)) instead of list (O(n))
large_collection = set(range(1000000))
print(999999 in large_collection)  # Very fast!
```

### Interview Insight: Why This Matters

Understanding data types helps you:

- Choose the right data structure for performance
- Avoid common pitfalls (mutability, type conversion errors)
- Write memory-efficient code
- Debug type-related errors quickly

Common interview questions from this section:

- "Explain the difference between list and tuple. When would you use each?"
- "What are mutable vs immutable types in Python?"
- "How does Python handle integer overflow?"
- "Explain dictionary implementation and time complexity of operations"
- "What's the difference between `is` and `==`?"
- "How do you remove duplicates from a list while preserving order?"

---

## Control Flow (if, loops, match)

### Overview

Control flow statements determine the order in which code executes. Python provides conditional statements, loops, and pattern matching to control program flow.

### Conditional Statements

#### The `if` Statement

```python
# Basic if
x = 10
if x > 5:
    print("x is greater than 5")

# if-else
if x > 15:
    print("x is greater than 15")
else:
    print("x is not greater than 15")

# if-elif-else
score = 85
if score >= 90:
    grade = 'A'
elif score >= 80:
    grade = 'B'
elif score >= 70:
    grade = 'C'
elif score >= 60:
    grade = 'D'
else:
    grade = 'F'
print(f"Grade: {grade}")

# Multiple conditions
age = 25
citizen = True
if age >= 18 and citizen:
    print("Eligible to vote")

# Nested if
x, y = 10, 20
if x > 5:
    if y > 15:
        print("Both conditions met")
```

```mermaid
flowchart TD
    A[Start] --> B{Condition}
    B -->|True| C[Execute if block]
    B -->|False| D{elif Condition}
    D -->|True| E[Execute elif block]
    D -->|False| F{Another elif?}
    F -->|Yes| D
    F -->|No| G[Execute else block]
    C --> H[Continue]
    E --> H
    G --> H

    style C fill:#90EE90
    style E fill:#FFE6A0
    style G fill:#FFB6C6
```

#### Ternary Operator (Conditional Expression)

```python
# Syntax: value_if_true if condition else value_if_false
x = 10
result = "positive" if x > 0 else "non-positive"

# Nested ternary (avoid if possible - hard to read)
x = 0
result = "positive" if x > 0 else "zero" if x == 0 else "negative"

# Practical use: default values
name = input("Enter name: ")
display_name = name if name else "Guest"

# In function calls
print("Even" if 10 % 2 == 0 else "Odd")
```

#### Truthiness in Conditionals

```python
# Falsy values: False, None, 0, 0.0, '', [], {}, set()
# Everything else is truthy

# Check if list is empty
my_list = []
if my_list:  # Pythonic way
    print("List has items")
else:
    print("List is empty")

# Don't do this (unpythonic)
if len(my_list) == 0:  # Works but not idiomatic
    print("List is empty")

# Check if string is non-empty
text = ""
if text:
    print("Text is not empty")

# Check for None
value = None
if value is None:  # Use 'is' for None
    print("Value is None")

# Walrus operator (Python 3.8+) - assignment in condition
if (n := len([1, 2, 3, 4])) > 3:
    print(f"List has {n} items")
```

### Loops

#### The `for` Loop

Iterates over sequences (lists, tuples, strings, etc.).

```python
# Basic for loop
fruits = ['apple', 'banana', 'cherry']
for fruit in fruits:
    print(fruit)

# Iterate over string
for char in "Python":
    print(char)

# Iterate over range
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# Range with start, stop, step
for i in range(2, 10, 2):
    print(i)  # 2, 4, 6, 8

# Iterate with index using enumerate
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")

# Start enumeration from 1
for index, fruit in enumerate(fruits, start=1):
    print(f"{index}. {fruit}")

# Iterate over dictionary
person = {'name': 'Alice', 'age': 30, 'city': 'NYC'}

# Keys (default)
for key in person:
    print(key)

# Values
for value in person.values():
    print(value)

# Key-value pairs
for key, value in person.items():
    print(f"{key}: {value}")

# Iterate over multiple sequences with zip
names = ['Alice', 'Bob', 'Charlie']
ages = [30, 25, 35]
for name, age in zip(names, ages):
    print(f"{name} is {age} years old")

# Nested loops
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
for row in matrix:
    for element in row:
        print(element, end=' ')
    print()  # New line after each row
```

#### The `while` Loop

Repeats as long as condition is true.

```python
# Basic while loop
count = 0
while count < 5:
    print(count)
    count += 1

# Input validation
while True:
    user_input = input("Enter 'quit' to exit: ")
    if user_input == 'quit':
        break
    print(f"You entered: {user_input}")

# Sentinel-controlled loop
total = 0
number = int(input("Enter number (0 to stop): "))
while number != 0:
    total += number
    number = int(input("Enter number (0 to stop): "))
print(f"Total: {total}")

# Be careful with infinite loops!
# while True:
#     print("This runs forever!")  # Don't do this without a break
```

```mermaid
flowchart TD
    A[Start] --> B{Condition True?}
    B -->|Yes| C[Execute loop body]
    C --> D[Update loop variable]
    D --> B
    B -->|No| E[Exit loop]

    style C fill:#90EE90
    style E fill:#FFB6C6
```

#### Loop Control Statements

```python
# break - exit loop immediately
for i in range(10):
    if i == 5:
        break  # Loop terminates when i is 5
    print(i)  # Prints 0, 1, 2, 3, 4

# continue - skip current iteration
for i in range(10):
    if i % 2 == 0:
        continue  # Skip even numbers
    print(i)  # Prints 1, 3, 5, 7, 9

# else clause - executes if loop completes normally (no break)
for i in range(5):
    print(i)
else:
    print("Loop completed normally")  # This executes

# With break - else doesn't execute
for i in range(5):
    if i == 3:
        break
    print(i)
else:
    print("This won't print")  # Skipped because of break

# pass - placeholder (does nothing)
for i in range(5):
    pass  # TODO: implement later
```

#### Practical Loop Patterns

```python
# 1. Find first element matching condition
numbers = [1, 3, 5, 7, 8, 10, 12]
for num in numbers:
    if num % 2 == 0:
        print(f"First even number: {num}")
        break
else:
    print("No even numbers found")

# 2. Count occurrences
text = "hello world"
count = 0
for char in text:
    if char == 'l':
        count += 1
print(f"'l' appears {count} times")

# Better: use count method
print(text.count('l'))

# 3. Build a new list (list comprehension is better)
squares = []
for x in range(10):
    squares.append(x**2)

# Better:
squares = [x**2 for x in range(10)]

# 4. Iterate in reverse
for i in range(9, -1, -1):
    print(i)  # 9, 8, 7, ..., 0

# Or use reversed()
for num in reversed([1, 2, 3, 4, 5]):
    print(num)  # 5, 4, 3, 2, 1

# 5. Parallel iteration with multiple lists
names = ['Alice', 'Bob', 'Charlie']
ages = [30, 25, 35]
cities = ['NYC', 'LA', 'Chicago']

for name, age, city in zip(names, ages, cities):
    print(f"{name}, {age}, from {city}")
```

### Pattern Matching (Python 3.10+)

The `match` statement provides structural pattern matching.

```python
# Basic match-case (like switch in other languages)
def http_status(status):
    match status:
        case 200:
            return "OK"
        case 404:
            return "Not Found"
        case 500:
            return "Internal Server Error"
        case _:  # Default case (wildcard)
            return "Unknown status"

print(http_status(200))  # "OK"
print(http_status(999))  # "Unknown status"

# Pattern matching with multiple values
def day_type(day):
    match day:
        case "Saturday" | "Sunday":
            return "Weekend"
        case "Monday" | "Tuesday" | "Wednesday" | "Thursday" | "Friday":
            return "Weekday"
        case _:
            return "Invalid day"

# Matching sequences
def process_command(command):
    match command:
        case ["quit"]:
            print("Quitting...")
        case ["load", filename]:
            print(f"Loading {filename}")
        case ["save", filename]:
            print(f"Saving to {filename}")
        case ["load", *files]:  # Match multiple items
            print(f"Loading multiple files: {files}")
        case _:
            print("Unknown command")

process_command(["load", "data.txt"])  # Loading data.txt
process_command(["load", "a.txt", "b.txt"])  # Loading multiple files: ['a.txt', 'b.txt']

# Matching dictionaries
def process_user(user):
    match user:
        case {"name": name, "age": age} if age >= 18:
            print(f"{name} is an adult")
        case {"name": name, "age": age}:
            print(f"{name} is a minor")
        case {"name": name}:
            print(f"Age not provided for {name}")
        case _:
            print("Invalid user data")

process_user({"name": "Alice", "age": 30})  # Alice is an adult

# Matching objects/classes
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

def describe_point(point):
    match point:
        case Point(x=0, y=0):
            print("Origin")
        case Point(x=0, y=y):
            print(f"On Y-axis at {y}")
        case Point(x=x, y=0):
            print(f"On X-axis at {x}")
        case Point(x=x, y=y) if x == y:
            print(f"On diagonal at ({x}, {y})")
        case Point(x=x, y=y):
            print(f"At ({x}, {y})")

describe_point(Point(0, 0))    # Origin
describe_point(Point(0, 5))    # On Y-axis at 5
describe_point(Point(3, 3))    # On diagonal at (3, 3)
```

```mermaid
flowchart TD
    A[match expression] --> B{case 1?}
    B -->|Match| C[Execute case 1]
    B -->|No match| D{case 2?}
    D -->|Match| E[Execute case 2]
    D -->|No match| F{case 3?}
    F -->|Match| G[Execute case 3]
    F -->|No match| H{case _ default?}
    H -->|Yes| I[Execute default]
    H -->|No| J[No match]

    C --> K[End]
    E --> K
    G --> K
    I --> K
    J --> K

    style C fill:#90EE90
    style E fill:#90EE90
    style G fill:#90EE90
    style I fill:#FFE6A0
    style J fill:#FFB6C6
```

### Comprehensions (Compact Control Flow)

#### List Comprehension

```python
# Basic list comprehension
squares = [x**2 for x in range(10)]
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# With condition
evens = [x for x in range(10) if x % 2 == 0]
# [0, 2, 4, 6, 8]

# With if-else (ternary)
labels = ["even" if x % 2 == 0 else "odd" for x in range(5)]
# ['even', 'odd', 'even', 'odd', 'even']

# Nested comprehension
matrix = [[i*j for j in range(3)] for i in range(3)]
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]

# Flatten nested list
nested = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [num for row in nested for num in row]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

#### Dictionary Comprehension

```python
# Basic dict comprehension
squares = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Swap keys and values
original = {'a': 1, 'b': 2, 'c': 3}
swapped = {v: k for k, v in original.items()}
# {1: 'a', 2: 'b', 3: 'c'}

# With condition
scores = {'Alice': 85, 'Bob': 92, 'Charlie': 78, 'David': 95}
high_scores = {name: score for name, score in scores.items() if score >= 90}
# {'Bob': 92, 'David': 95}
```

#### Set Comprehension

```python
# Basic set comprehension
squares = {x**2 for x in range(-5, 6)}
# {0, 1, 4, 9, 16, 25} (duplicates removed)

# Extract unique characters
text = "hello world"
unique_chars = {char for char in text if char != ' '}
# {'h', 'e', 'l', 'o', 'w', 'r', 'd'}
```

#### Generator Expression

```python
# Generator expression (uses parentheses)
squares_gen = (x**2 for x in range(10))
# <generator object at 0x...>

# Lazy evaluation - values computed on demand
for square in squares_gen:
    print(square)

# Memory efficient for large datasets
sum_of_squares = sum(x**2 for x in range(1000000))  # No list created!
```

### Practical Example: Control Flow in Action

```python
# Example: Process student grades
students = [
    {"name": "Alice", "score": 85},
    {"name": "Bob", "score": 92},
    {"name": "Charlie", "score": 78},
    {"name": "David", "score": 65},
    {"name": "Eve", "score": 95}
]

# Task 1: Assign grades
for student in students:
    score = student["score"]
    if score >= 90:
        grade = 'A'
    elif score >= 80:
        grade = 'B'
    elif score >= 70:
        grade = 'C'
    elif score >= 60:
        grade = 'D'
    else:
        grade = 'F'
    student["grade"] = grade
    print(f"{student['name']}: {score} ({grade})")

# Task 2: Find top scorer
top_student = None
top_score = 0
for student in students:
    if student["score"] > top_score:
        top_score = student["score"]
        top_student = student["name"]
print(f"\nTop student: {top_student} with {top_score}")

# Task 3: Calculate average (with comprehension)
average = sum(s["score"] for s in students) / len(students)
print(f"Average score: {average:.2f}")

# Task 4: Students above average
above_avg = [s["name"] for s in students if s["score"] > average]
print(f"Above average: {', '.join(above_avg)}")
```

### Interview Insight: Why This Matters

Understanding control flow helps you:

- Write clean, readable code
- Choose the right loop construct
- Optimize performance (e.g., break early)
- Use comprehensions for concise code

Common interview questions from this section:

- "What's the difference between `break` and `continue`?"
- "Explain the `else` clause in loops"
- "When would you use a `while` loop vs a `for` loop?"
- "What are list comprehensions and when should you use them?"
- "Explain pattern matching in Python 3.10+"
- "How do you iterate over multiple lists simultaneously?"

---

## Functions and Functional Concepts

### Overview

Functions are reusable blocks of code that perform specific tasks. Python supports various function types and functional programming concepts.

### Function Basics

#### Defining Functions

```python
# Basic function
def greet():
    print("Hello!")

greet()  # Call the function

# Function with parameters
def greet_person(name):
    print(f"Hello, {name}!")

greet_person("Alice")

# Function with return value
def add(a, b):
    return a + b

result = add(3, 5)
print(result)  # 8

# Multiple return values (returns tuple)
def get_coordinates():
    return 10, 20

x, y = get_coordinates()  # Unpacking
print(x, y)  # 10 20

# Early return
def is_even(n):
    if n % 2 == 0:
        return True
    return False

# More concise
def is_even(n):
    return n % 2 == 0
```

#### Function Parameters

```python
# Positional parameters
def greet(first_name, last_name):
    print(f"Hello, {first_name} {last_name}!")

greet("John", "Doe")

# Keyword arguments
greet(last_name="Doe", first_name="John")  # Order doesn't matter

# Default parameters
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Alice")           # Hello, Alice!
greet("Bob", "Hi")       # Hi, Bob!
greet("Charlie", greeting="Hey")  # Hey, Charlie!

# IMPORTANT: Default values evaluated once at function definition
def append_to_list(item, lst=[]):  # DANGEROUS!
    lst.append(item)
    return lst

print(append_to_list(1))  # [1]
print(append_to_list(2))  # [1, 2] - Same list!

# Correct way:
def append_to_list(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst

print(append_to_list(1))  # [1]
print(append_to_list(2))  # [2] - New list!

# *args - variable positional arguments
def sum_all(*args):
    return sum(args)

print(sum_all(1, 2, 3))        # 6
print(sum_all(1, 2, 3, 4, 5))  # 15

# **kwargs - variable keyword arguments
def print_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Alice", age=30, city="NYC")

# Combining all parameter types
def complex_function(pos1, pos2, *args, kwonly1, kwonly2="default", **kwargs):
    print(f"Positional: {pos1}, {pos2}")
    print(f"*args: {args}")
    print(f"Keyword-only: {kwonly1}, {kwonly2}")
    print(f"**kwargs: {kwargs}")

complex_function(1, 2, 3, 4, kwonly1="required", extra="extra_value")

# Position-only parameters (Python 3.8+)
def func(a, b, /, c, d, *, e, f):
    # a, b are position-only (before /)
    # c, d can be positional or keyword
    # e, f are keyword-only (after *)
    pass

func(1, 2, 3, 4, e=5, f=6)       # Valid
# func(a=1, b=2, c=3, d=4, e=5, f=6)  # Invalid - a, b position-only
```

```mermaid
graph TD
    A[Function Parameters] --> B[Positional]
    A --> C[Default]
    A --> D[*args]
    A --> E[Keyword-only]
    A --> F[**kwargs]

    B --> B1["Must be provided<br/>Order matters"]
    C --> C1["Optional<br/>Has default value"]
    D --> D1["Variable positional<br/>Tuple of args"]
    E --> E1["After * or *args<br/>Must use name"]
    F --> F1["Variable keyword<br/>Dict of kwargs"]

    style B1 fill:#FFB6C6
    style C1 fill:#FFE6A0
    style D1 fill:#E6F3FF
    style E1 fill:#E6FFE6
    style F1 fill:#F0E6FF
```

#### Function Annotations (Type Hints)

```python
# Type hints (Python 3.5+)
def greet(name: str) -> str:
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    return a + b

# Complex types
from typing import List, Dict, Tuple, Optional, Union

def process_items(items: List[int]) -> int:
    return sum(items)

def get_user(user_id: int) -> Optional[Dict[str, str]]:
    # Returns dict or None
    if user_id == 1:
        return {"name": "Alice", "email": "alice@example.com"}
    return None

def handle_input(value: Union[int, str]) -> str:
    # Accepts int or str
    return str(value)

# Note: Type hints are NOT enforced at runtime!
# Use tools like mypy for static type checking
```

### Scope and Lifetime

#### LEGB Rule (Revisited with Functions)

```python
x = "global"

def outer():
    x = "enclosing"

    def inner():
        x = "local"
        print(f"Inner: {x}")  # local

    inner()
    print(f"Outer: {x}")  # enclosing

outer()
print(f"Global: {x}")  # global

# Modifying enclosing scope with nonlocal
def counter():
    count = 0

    def increment():
        nonlocal count  # Modify enclosing scope
        count += 1
        return count

    return increment

c = counter()
print(c())  # 1
print(c())  # 2
print(c())  # 3

# Modifying global scope with global
counter = 0

def increment():
    global counter  # Modify global variable
    counter += 1

increment()
increment()
print(counter)  # 2
```

### Lambda Functions

Anonymous, single-expression functions.

```python
# Basic lambda
add = lambda x, y: x + y
print(add(3, 5))  # 8

# Lambda with single parameter
square = lambda x: x**2
print(square(4))  # 16

# Common use: with higher-order functions
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, numbers))
print(squared)  # [1, 4, 9, 16, 25]

# Filter with lambda
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4]

# Sort with lambda (custom key)
students = [
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 20},
    {"name": "Charlie", "age": 30}
]
students.sort(key=lambda s: s["age"])
print([s["name"] for s in students])  # ['Bob', 'Alice', 'Charlie']

# Lambda limitations: single expression only, no statements
# Can't do: lambda x: print(x)  # Invalid
# Can't do: lambda x: if x > 0: return x  # Invalid

# When to use lambda:
# - Short, simple operations
# - As argument to higher-order functions
# - When defining a named function is overkill

# When NOT to use lambda:
# - Complex logic (use def instead)
# - When you need multiple statements
# - When readability suffers
```

### Higher-Order Functions

Functions that take functions as arguments or return functions.

```python
# map() - apply function to each element
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, numbers))
print(squared)  # [1, 4, 9, 16, 25]

# Multiple iterables
list1 = [1, 2, 3]
list2 = [4, 5, 6]
sums = list(map(lambda x, y: x + y, list1, list2))
print(sums)  # [5, 7, 9]

# filter() - keep elements where function returns True
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4, 6, 8, 10]

# reduce() - apply function cumulatively
from functools import reduce
numbers = [1, 2, 3, 4, 5]
product = reduce(lambda x, y: x * y, numbers)
print(product)  # 120 (1*2*3*4*5)

# Function returning function
def make_multiplier(n):
    def multiplier(x):
        return x * n
    return multiplier

times_two = make_multiplier(2)
times_three = make_multiplier(3)

print(times_two(5))    # 10
print(times_three(5))  # 15

# Function as parameter
def apply_operation(x, y, operation):
    return operation(x, y)

print(apply_operation(5, 3, lambda a, b: a + b))  # 8
print(apply_operation(5, 3, lambda a, b: a * b))  # 15
```

### Decorators

Functions that modify the behavior of other functions.

```python
# Basic decorator
def my_decorator(func):
    def wrapper():
        print("Before function call")
        func()
        print("After function call")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
# Output:
# Before function call
# Hello!
# After function call

# Equivalent to:
# say_hello = my_decorator(say_hello)

# Decorator with arguments
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Finished {func.__name__}")
        return result
    return wrapper

@my_decorator
def add(a, b):
    return a + b

result = add(3, 5)  # Prints decorating messages
print(result)  # 8

# Preserve function metadata with functools.wraps
from functools import wraps

def my_decorator(func):
    @wraps(func)  # Preserves func.__name__, func.__doc__, etc.
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

# Decorator with parameters
def repeat(times):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
# Output (3 times):
# Hello, Alice!
# Hello, Alice!
# Hello, Alice!

# Common decorator patterns

# 1. Timing decorator
import time

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)

slow_function()  # Prints execution time

# 2. Caching decorator (memoization)
def memoize(func):
    cache = {}
    @wraps(func)
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrapper

@memoize
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(100))  # Fast due to caching!

# Or use built-in functools.lru_cache
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

```mermaid
sequenceDiagram
    participant C as Caller
    participant D as Decorator
    participant F as Original Function

    C->>D: Call decorated function
    D->>D: Pre-processing
    D->>F: Call original function
    F-->>D: Return result
    D->>D: Post-processing
    D-->>C: Return final result
```

### Closures

Functions that remember values from enclosing scope.

```python
# Basic closure
def outer(x):
    def inner(y):
        return x + y  # 'inner' remembers 'x'
    return inner

add_five = outer(5)
print(add_five(3))  # 8
print(add_five(10)) # 15

# Closure retains state
def make_counter():
    count = 0

    def counter():
        nonlocal count
        count += 1
        return count

    return counter

counter1 = make_counter()
counter2 = make_counter()

print(counter1())  # 1
print(counter1())  # 2
print(counter2())  # 1 (separate state)
print(counter1())  # 3

# Checking closure variables
def outer(x):
    def inner(y):
        return x + y
    return inner

f = outer(5)
print(f.__closure__)  # (<cell at 0x...: int object at 0x...>,)
print(f.__closure__[0].cell_contents)  # 5
```

### Generators (Preview)

Functions that yield values instead of returning.

```python
# Generator function
def count_up_to(n):
    count = 1
    while count <= n:
        yield count  # Yields value and pauses
        count += 1

counter = count_up_to(5)
print(next(counter))  # 1
print(next(counter))  # 2

# Or use in loop
for num in count_up_to(3):
    print(num)  # 1, 2, 3

# Generator expression (covered more in later section)
squares = (x**2 for x in range(10))
```

### Practical Example: Function Composition

```python
# Example: Data processing pipeline

def clean_text(text):
    """Remove extra whitespace"""
    return ' '.join(text.split())

def lowercase(text):
    """Convert to lowercase"""
    return text.lower()

def remove_punctuation(text):
    """Remove punctuation"""
    import string
    return text.translate(str.maketrans('', '', string.punctuation))

# Compose functions
def process_text(text):
    text = clean_text(text)
    text = lowercase(text)
    text = remove_punctuation(text)
    return text

text = "  Hello,   WORLD!  How  are   you?  "
result = process_text(text)
print(result)  # "hello world how are you"

# Generic function composition
def compose(*functions):
    def composed(value):
        for func in reversed(functions):
            value = func(value)
        return value
    return composed

process = compose(remove_punctuation, lowercase, clean_text)
result = process("  Hello,   WORLD!  ")
print(result)  # "hello world"
```

### Interview Insight: Why This Matters

Understanding functions helps you:

- Write modular, reusable code
- Understand scope and closures
- Use functional programming patterns
- Create flexible, composable APIs

Common interview questions from this section:

- "Explain the difference between parameters and arguments"
- "What are \*args and \*\*kwargs?"
- "What is a closure? Give an example"
- "Explain how decorators work"
- "What's the difference between a function and a lambda?"
- "What are higher-order functions?"
- "Explain the LEGB rule for scope resolution"

---

## Object-Oriented Programming

### Overview

Object-Oriented Programming (OOP) organizes code into objects that combine data (attributes) and behavior (methods). Python fully supports OOP with classes and objects.

### Classes and Objects

#### Basic Class Definition

```python
# Simple class
class Dog:
    # Class attribute (shared by all instances)
    species = "Canis familiaris"

    # Constructor (initializer)
    def __init__(self, name, age):
        # Instance attributes (unique to each instance)
        self.name = name
        self.age = age

    # Instance method
    def bark(self):
        return f"{self.name} says Woof!"

    # Method with parameters
    def celebrate_birthday(self):
        self.age += 1
        return f"{self.name} is now {self.age} years old!"

# Create objects (instances)
dog1 = Dog("Buddy", 3)
dog2 = Dog("Lucy", 5)

# Access attributes
print(dog1.name)  # "Buddy"
print(dog1.age)   # 3
print(dog1.species)  # "Canis familiaris"

# Call methods
print(dog1.bark())  # "Buddy says Woof!"
print(dog1.celebrate_birthday())  # "Buddy is now 4 years old!"

# Class attributes are shared
print(dog1.species)  # "Canis familiaris"
print(dog2.species)  # "Canis familiaris"
Dog.species = "Dog"
print(dog1.species)  # "Dog" (changed for all instances)
```

```mermaid
classDiagram
    class Dog {
        +string species
        +string name
        +int age
        +__init__(name, age)
        +bark()
        +celebrate_birthday()
    }

    Dog <|-- dog1
    Dog <|-- dog2

    note for Dog "Class (Blueprint)"
    note for dog1 "Instance 1"
    note for dog2 "Instance 2"
```

#### The `self` Parameter

```python
# 'self' refers to the instance calling the method
class Counter:
    def __init__(self):
        self.count = 0

    def increment(self):
        self.count += 1  # Modifies THIS instance's count

    def get_count(self):
        return self.count

c1 = Counter()
c2 = Counter()

c1.increment()
c1.increment()
c2.increment()

print(c1.get_count())  # 2
print(c2.get_count())  # 1 (separate state)

# Note: 'self' is convention, not keyword
class Example:
    def method(this):  # Can use any name (but don't!)
        return this
```

#### Class Methods and Static Methods

```python
class MyClass:
    class_variable = 0

    def __init__(self, value):
        self.value = value

    # Instance method (needs self)
    def instance_method(self):
        return f"Instance value: {self.value}"

    # Class method (receives class as first parameter)
    @classmethod
    def class_method(cls):
        cls.class_variable += 1
        return f"Class variable: {cls.class_variable}"

    # Static method (doesn't receive self or cls)
    @staticmethod
    def static_method(x, y):
        return x + y

    # Alternative constructor using class method
    @classmethod
    def from_string(cls, string_value):
        return cls(int(string_value))

# Instance method requires instance
obj = MyClass(10)
print(obj.instance_method())  # "Instance value: 10"

# Class method can be called on class or instance
print(MyClass.class_method())  # "Class variable: 1"
print(obj.class_method())      # "Class variable: 2"

# Static method can be called on class or instance
print(MyClass.static_method(5, 3))  # 8
print(obj.static_method(5, 3))      # 8

# Alternative constructor
obj2 = MyClass.from_string("42")
print(obj2.value)  # 42
```

### Encapsulation and Access Control

Python doesn't have true private attributes, but uses conventions.

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner          # Public
        self._balance = balance     # Protected (convention: internal use)
        self.__account_id = "123"   # Private (name mangling)

    def deposit(self, amount):
        if amount > 0:
            self._balance += amount

    def get_balance(self):
        return self._balance

    def __str__(self):
        return f"Account of {self.owner}: ${self._balance}"

account = BankAccount("Alice", 1000)

# Public attribute - directly accessible
print(account.owner)  # "Alice"

# Protected attribute - accessible but shouldn't be used externally
print(account._balance)  # 1000 (works, but discouraged)

# Private attribute - name mangling
# print(account.__account_id)  # AttributeError
print(account._BankAccount__account_id)  # "123" (name mangled, but still accessible)

# Use methods to interact with data
account.deposit(500)
print(account.get_balance())  # 1500
```

### Properties (Getters and Setters)

```python
# Without properties (Java-style)
class Person:
    def __init__(self, name, age):
        self._name = name
        self._age = age

    def get_age(self):
        return self._age

    def set_age(self, age):
        if age < 0:
            raise ValueError("Age cannot be negative")
        self._age = age

# With properties (Pythonic)
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age  # Uses setter

    @property
    def age(self):
        """Getter"""
        return self._age

    @age.setter
    def age(self, value):
        """Setter with validation"""
        if value < 0:
            raise ValueError("Age cannot be negative")
        self._age = value

    @age.deleter
    def age(self):
        """Deleter"""
        del self._age

person = Person("Alice", 30)
print(person.age)  # 30 (uses getter)

person.age = 31    # Uses setter
print(person.age)  # 31

# person.age = -5  # ValueError: Age cannot be negative

# Read-only property (no setter)
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14159 * self.radius ** 2

    @property
    def diameter(self):
        return self.radius * 2

circle = Circle(5)
print(circle.area)      # 78.53975 (computed)
print(circle.diameter)  # 10
# circle.area = 100  # AttributeError (read-only)
```

### Inheritance

```python
# Base class (parent/superclass)
class Animal:
    def __init__(self, name, species):
        self.name = name
        self.species = species

    def make_sound(self):
        return "Some generic sound"

    def info(self):
        return f"{self.name} is a {self.species}"

# Derived class (child/subclass)
class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name, "Dog")  # Call parent constructor
        self.breed = breed

    # Override parent method
    def make_sound(self):
        return "Woof!"

    # Add new method
    def fetch(self):
        return f"{self.name} is fetching!"

class Cat(Animal):
    def __init__(self, name, indoor):
        super().__init__(name, "Cat")
        self.indoor = indoor

    def make_sound(self):
        return "Meow!"

# Using inheritance
dog = Dog("Buddy", "Golden Retriever")
cat = Cat("Whiskers", True)

print(dog.info())         # "Buddy is a Dog" (inherited method)
print(dog.make_sound())   # "Woof!" (overridden method)
print(dog.fetch())        # "Buddy is fetching!" (new method)

print(cat.make_sound())   # "Meow!"

# Check inheritance
print(isinstance(dog, Dog))     # True
print(isinstance(dog, Animal))  # True
print(isinstance(dog, Cat))     # False

print(issubclass(Dog, Animal))  # True
print(issubclass(Dog, Cat))     # False
```

```mermaid
classDiagram
    class Animal {
        +string name
        +string species
        +__init__(name, species)
        +make_sound()
        +info()
    }

    class Dog {
        +string breed
        +__init__(name, breed)
        +make_sound()
        +fetch()
    }

    class Cat {
        +boolean indoor
        +__init__(name, indoor)
        +make_sound()
    }

    Animal <|-- Dog : inherits
    Animal <|-- Cat : inherits
```

#### Multiple Inheritance

```python
# Multiple inheritance (use with caution!)
class Flyable:
    def fly(self):
        return "Flying!"

class Swimmable:
    def swim(self):
        return "Swimming!"

class Duck(Flyable, Swimmable):
    def __init__(self, name):
        self.name = name

    def quack(self):
        return "Quack!"

duck = Duck("Donald")
print(duck.fly())    # "Flying!" (from Flyable)
print(duck.swim())   # "Swimming!" (from Swimmable)
print(duck.quack())  # "Quack!" (own method)

# Method Resolution Order (MRO)
print(Duck.__mro__)
# (<class 'Duck'>, <class 'Flyable'>, <class 'Swimmable'>, <class 'object'>)

# Diamond problem
class A:
    def method(self):
        return "A"

class B(A):
    def method(self):
        return "B"

class C(A):
    def method(self):
        return "C"

class D(B, C):
    pass

d = D()
print(d.method())  # "B" (follows MRO: D -> B -> C -> A)
print(D.__mro__)
```

### Polymorphism

```python
# Polymorphism - different classes with same interface
class Shape:
    def area(self):
        pass

    def perimeter(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14159 * self.radius ** 2

    def perimeter(self):
        return 2 * 3.14159 * self.radius

# Polymorphic function
def print_shape_info(shape):
    print(f"Area: {shape.area()}")
    print(f"Perimeter: {shape.perimeter()}")

shapes = [Rectangle(5, 10), Circle(7)]

for shape in shapes:
    print_shape_info(shape)  # Works for any shape!

# Duck typing - "If it walks like a duck and quacks like a duck..."
class NotAShape:
    def area(self):
        return 42

    def perimeter(self):
        return 84

# Works even though NotAShape doesn't inherit from Shape!
print_shape_info(NotAShape())
```

### Magic Methods (Dunder Methods)

Special methods that define behavior for built-in operations.

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    # String representation
    def __str__(self):
        """For print() and str()"""
        return f"Vector({self.x}, {self.y})"

    def __repr__(self):
        """For repr() and interactive console"""
        return f"Vector({self.x}, {self.y})"

    # Arithmetic operators
    def __add__(self, other):
        """v1 + v2"""
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other):
        """v1 - v2"""
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, scalar):
        """v * scalar"""
        return Vector(self.x * scalar, self.y * scalar)

    # Comparison operators
    def __eq__(self, other):
        """v1 == v2"""
        return self.x == other.x and self.y == other.y

    def __lt__(self, other):
        """v1 < v2"""
        return self.magnitude() < other.magnitude()

    # Other magic methods
    def __len__(self):
        """len(v)"""
        return 2  # 2D vector

    def __getitem__(self, index):
        """v[0], v[1]"""
        if index == 0:
            return self.x
        elif index == 1:
            return self.y
        else:
            raise IndexError("Vector index out of range")

    def __call__(self):
        """v() - make object callable"""
        return self.magnitude()

    def magnitude(self):
        return (self.x**2 + self.y**2)**0.5

# Using magic methods
v1 = Vector(3, 4)
v2 = Vector(1, 2)

print(v1)           # Vector(3, 4) (__str__)
print(v1 + v2)      # Vector(4, 6) (__add__)
print(v1 - v2)      # Vector(2, 2) (__sub__)
print(v1 * 2)       # Vector(6, 8) (__mul__)
print(v1 == v2)     # False (__eq__)
print(v1 < v2)      # False (__lt__)
print(len(v1))      # 2 (__len__)
print(v1[0])        # 3 (__getitem__)
print(v1())         # 5.0 (__call__)

# Common magic methods:
# __init__: Constructor
# __str__: String for users
# __repr__: String for developers
# __add__, __sub__, __mul__, __truediv__: Arithmetic
# __eq__, __ne__, __lt__, __le__, __gt__, __ge__: Comparison
# __len__: Length
# __getitem__, __setitem__, __delitem__: Indexing
# __iter__, __next__: Iteration
# __enter__, __exit__: Context managers
# __call__: Make object callable
```

### Abstract Base Classes

```python
from abc import ABC, abstractmethod

# Abstract class (cannot be instantiated)
class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

    # Can have concrete methods too
    def description(self):
        return f"I am a shape with area {self.area()}"

# Cannot instantiate abstract class
# shape = Shape()  # TypeError

# Must implement all abstract methods
class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

rect = Rectangle(5, 10)
print(rect.area())         # 50
print(rect.description())  # "I am a shape with area 50"
```

### Dataclasses (Python 3.7+)

Simplify class creation for data-holding classes.

```python
from dataclasses import dataclass, field
from typing import List

# Without dataclass
class PersonOld:
    def __init__(self, name, age, hobbies):
        self.name = name
        self.age = age
        self.hobbies = hobbies

    def __repr__(self):
        return f"Person(name={self.name}, age={self.age}, hobbies={self.hobbies})"

    def __eq__(self, other):
        return self.name == other.name and self.age == other.age

# With dataclass
@dataclass
class Person:
    name: str
    age: int
    hobbies: List[str] = field(default_factory=list)  # Mutable default

    def greet(self):
        return f"Hi, I'm {self.name}!"

# Automatic __init__, __repr__, __eq__, etc.
p1 = Person("Alice", 30, ["reading", "coding"])
p2 = Person("Bob", 25)

print(p1)  # Person(name='Alice', age=30, hobbies=['reading', 'coding'])
print(p1 == p2)  # False
print(p1.greet())  # "Hi, I'm Alice!"

# Additional options
@dataclass(frozen=True)  # Immutable
class ImmutablePerson:
    name: str
    age: int

# ip = ImmutablePerson("Charlie", 35)
# ip.age = 36  # FrozenInstanceError

@dataclass(order=True)  # Add comparison methods
class ComparablePerson:
    name: str
    age: int = field(compare=True)
    email: str = field(compare=False)  # Exclude from comparison

p1 = ComparablePerson("Alice", 30, "alice@example.com")
p2 = ComparablePerson("Bob", 25, "bob@example.com")
print(p1 > p2)  # True (compares by age)
```

### Practical Example: Bank Account System

```python
from abc import ABC, abstractmethod
from datetime import datetime

class Account(ABC):
    """Abstract base class for bank accounts"""

    account_count = 0  # Class variable

    def __init__(self, owner, balance=0):
        self.owner = owner
        self._balance = balance
        self._transactions = []
        Account.account_count += 1
        self.account_number = Account.account_count

    @abstractmethod
    def withdraw(self, amount):
        """Must be implemented by subclasses"""
        pass

    def deposit(self, amount):
        if amount > 0:
            self._balance += amount
            self._add_transaction("Deposit", amount)
            return True
        return False

    def get_balance(self):
        return self._balance

    def _add_transaction(self, type, amount):
        self._transactions.append({
            'type': type,
            'amount': amount,
            'timestamp': datetime.now()
        })

    def transaction_history(self):
        return self._transactions

    def __str__(self):
        return f"Account #{self.account_number} ({self.owner}): ${self._balance:.2f}"

class SavingsAccount(Account):
    """Savings account with interest"""

    interest_rate = 0.02  # 2% interest

    def withdraw(self, amount):
        if amount > 0 and amount <= self._balance:
            self._balance -= amount
            self._add_transaction("Withdrawal", amount)
            return True
        return False

    def apply_interest(self):
        interest = self._balance * self.interest_rate
        self._balance += interest
        self._add_transaction("Interest", interest)
        return interest

class CheckingAccount(Account):
    """Checking account with overdraft protection"""

    def __init__(self, owner, balance=0, overdraft_limit=500):
        super().__init__(owner, balance)
        self.overdraft_limit = overdraft_limit

    def withdraw(self, amount):
        if amount > 0 and amount <= self._balance + self.overdraft_limit:
            self._balance -= amount
            self._add_transaction("Withdrawal", amount)
            return True
        return False

# Using the system
savings = SavingsAccount("Alice", 1000)
checking = CheckingAccount("Bob", 500, overdraft_limit=200)

print(savings)   # Account #1 (Alice): $1000.00
print(checking)  # Account #2 (Bob): $500.00

savings.deposit(500)
print(savings)   # Account #1 (Alice): $1500.00

savings.withdraw(200)
print(savings)   # Account #1 (Alice): $1300.00

interest = savings.apply_interest()
print(f"Interest earned: ${interest:.2f}")  # Interest earned: $26.00

checking.withdraw(600)  # Uses overdraft
print(checking)  # Account #2 (Bob): $-100.00

print(f"Total accounts created: {Account.account_count}")
```

### Interview Insight: Why This Matters

Understanding OOP helps you:

- Design scalable, maintainable systems
- Model real-world entities as code
- Use inheritance and composition effectively
- Implement design patterns

Common interview questions from this section:

- "Explain the four pillars of OOP (Encapsulation, Inheritance, Polymorphism, Abstraction)"
- "What's the difference between class and instance variables?"
- "Explain the difference between `__str__` and `__repr__`"
- "What is method overriding vs method overloading?"
- "Explain the difference between composition and inheritance"
- "What is the purpose of `@classmethod` and `@staticmethod`?"
- "What are magic/dunder methods?"
- "Explain the MRO (Method Resolution Order) in multiple inheritance"

---

## Error and Exception Handling

### Overview

Exceptions are runtime errors that disrupt normal program flow. Python provides a robust exception handling mechanism to catch and handle errors gracefully.

### Exception Hierarchy

Python exceptions are organized in a hierarchy:

```mermaid
graph TD
    A[BaseException] --> B[Exception]
    A --> C[SystemExit]
    A --> D[KeyboardInterrupt]

    B --> E[ArithmeticError]
    B --> F[LookupError]
    B --> G[ValueError]
    B --> H[TypeError]
    B --> I[NameError]
    B --> J[AttributeError]
    B --> K[IOError/OSError]

    E --> E1[ZeroDivisionError]
    E --> E2[OverflowError]

    F --> F1[IndexError]
    F --> F2[KeyError]

    style A fill:#FFB6C6
    style B fill:#FFE6A0
    style E fill:#E6F3FF
    style F fill:#E6F3FF
```

### Basic Exception Handling

#### try-except Block

```python
# Basic try-except
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero!")

# Without exception handling
# result = 10 / 0  # ZeroDivisionError: division by zero

# Catching exception object
try:
    result = int("hello")
except ValueError as e:
    print(f"Error: {e}")  # Error: invalid literal for int() with base 10: 'hello'

# Multiple except blocks
try:
    numbers = [1, 2, 3]
    print(numbers[5])
except IndexError:
    print("Index out of range!")
except ValueError:
    print("Invalid value!")

# Catch multiple exceptions in one block
try:
    value = int(input("Enter a number: "))
    result = 10 / value
except (ValueError, ZeroDivisionError) as e:
    print(f"Error: {e}")

# Generic exception catch (use sparingly!)
try:
    # Some risky operation
    pass
except Exception as e:
    print(f"An error occurred: {e}")

# Bare except (NOT recommended - catches everything, even KeyboardInterrupt)
try:
    pass
except:
    print("Something went wrong")  # Too broad!
```

#### try-except-else

```python
# else block - executes if no exception occurs
try:
    result = 10 / 2
except ZeroDivisionError:
    print("Cannot divide by zero!")
else:
    print(f"Result: {result}")  # Executes only if no exception

# Practical use: separate error handling from success logic
def read_file(filename):
    try:
        f = open(filename, 'r')
    except FileNotFoundError:
        print(f"File {filename} not found")
    else:
        # Only runs if file opened successfully
        content = f.read()
        f.close()
        return content
```

#### try-except-finally

```python
# finally block - always executes, regardless of exceptions
try:
    f = open("data.txt", "r")
    content = f.read()
except FileNotFoundError:
    print("File not found")
finally:
    print("This always executes")
    # f.close()  # Resource cleanup

# Common pattern: cleanup resources
def process_file(filename):
    f = None
    try:
        f = open(filename, 'r')
        data = f.read()
        # Process data
        return data
    except FileNotFoundError:
        print(f"File {filename} not found")
        return None
    except Exception as e:
        print(f"Error processing file: {e}")
        return None
    finally:
        if f:
            f.close()  # Ensures file is closed

# Better: Use context manager (covered later)
```

### Raising Exceptions

#### raise Statement

```python
# Raise built-in exception
def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("Cannot divide by zero!")
    return a / b

# try:
#     divide(10, 0)
# except ZeroDivisionError as e:
#     print(e)  # Cannot divide by zero!

# Raise with no argument (re-raise current exception)
def process_data(data):
    try:
        # Some operation
        result = int(data)
    except ValueError:
        print("Logging error...")
        raise  # Re-raises the ValueError

# try:
#     process_data("invalid")
# except ValueError:
#     print("Caught re-raised exception")

# Raise from another exception (exception chaining)
def load_config():
    try:
        with open("config.json") as f:
            import json
            return json.load(f)
    except FileNotFoundError as e:
        raise ValueError("Configuration file missing") from e

# try:
#     load_config()
# except ValueError as e:
#     print(e)
#     print(e.__cause__)  # Original FileNotFoundError
```

### Custom Exceptions

```python
# Basic custom exception
class CustomError(Exception):
    pass

raise CustomError("Something went wrong")

# Custom exception with additional attributes
class ValidationError(Exception):
    def __init__(self, message, field):
        super().__init__(message)
        self.field = field

def validate_age(age):
    if age < 0:
        raise ValidationError("Age cannot be negative", field="age")
    if age > 150:
        raise ValidationError("Age is unrealistic", field="age")

try:
    validate_age(-5)
except ValidationError as e:
    print(f"Validation error in field '{e.field}': {e}")

# Custom exception hierarchy
class DatabaseError(Exception):
    """Base class for database exceptions"""
    pass

class ConnectionError(DatabaseError):
    """Database connection failed"""
    pass

class QueryError(DatabaseError):
    """Database query failed"""
    pass

# Catch specific or base exception
try:
    raise QueryError("Invalid SQL syntax")
except DatabaseError as e:  # Catches all database errors
    print(f"Database error: {e}")
```

### Common Exception Patterns

```python
# 1. EAFP (Easier to Ask for Forgiveness than Permission) - Pythonic
# Try operation, handle exception if it fails
try:
    value = my_dict["key"]
except KeyError:
    value = None

# vs LBYL (Look Before You Leap) - Less Pythonic
if "key" in my_dict:
    value = my_dict["key"]
else:
    value = None

# 2. Converting exceptions
def safe_int(value):
    try:
        return int(value)
    except ValueError:
        return 0  # Default value instead of exception

# 3. Cleanup with try-finally
lock = threading.Lock()
lock.acquire()
try:
    # Critical section
    pass
finally:
    lock.release()  # Always release lock

# 4. Retrying with exceptions
def fetch_data_with_retry(url, max_retries=3):
    for attempt in range(max_retries):
        try:
            # Fetch data
            return data
        except NetworkError:
            if attempt == max_retries - 1:
                raise  # Re-raise on last attempt
            time.sleep(1)  # Wait before retry
```

### Context Managers (with statement)

Context managers ensure proper resource management.

```python
# Without context manager
f = open("file.txt", "r")
try:
    content = f.read()
finally:
    f.close()

# With context manager (better!)
with open("file.txt", "r") as f:
    content = f.read()
# File automatically closed after with block

# Multiple context managers
with open("input.txt", "r") as infile, open("output.txt", "w") as outfile:
    content = infile.read()
    outfile.write(content.upper())

# Creating custom context manager (class-based)
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        """Called when entering with block"""
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        """Called when exiting with block"""
        if self.file:
            self.file.close()
        # Return False to propagate exceptions, True to suppress
        return False

with FileManager("test.txt", "w") as f:
    f.write("Hello, World!")

# Creating context manager (function-based)
from contextlib import contextmanager

@contextmanager
def file_manager(filename, mode):
    try:
        f = open(filename, mode)
        yield f  # Provides value to 'as' clause
    finally:
        f.close()

with file_manager("test.txt", "r") as f:
    content = f.read()

# Suppress exceptions with contextmanager
from contextlib import suppress

# Instead of:
try:
    os.remove("file.txt")
except FileNotFoundError:
    pass

# Use:
with suppress(FileNotFoundError):
    os.remove("file.txt")
```

```mermaid
sequenceDiagram
    participant Code
    participant ContextManager
    participant Resource

    Code->>ContextManager: Enter with block
    ContextManager->>Resource: __enter__() - Acquire resource
    Resource-->>ContextManager: Return resource
    ContextManager-->>Code: Provide resource
    Code->>Code: Use resource

    alt Exception occurs
        Code->>ContextManager: Exception raised
        ContextManager->>Resource: __exit__() with exception info
    else No exception
        Code->>ContextManager: Exit with block normally
        ContextManager->>Resource: __exit__() with no exception
    end

    Resource-->>ContextManager: Cleanup complete
    ContextManager-->>Code: Continue execution
```

### Assertions

Assertions are debugging aids - not for handling runtime errors.

```python
# Basic assertion
x = 5
assert x > 0, "x must be positive"

# Assertions can be disabled with -O flag (python -O script.py)
# So NEVER use assertions for:
# - Input validation
# - Error handling
# - Business logic

# Bad: Using assertion for validation
def withdraw(amount):
    assert amount > 0  # BAD! Can be disabled
    # Process withdrawal

# Good: Raise exception for validation
def withdraw(amount):
    if amount <= 0:
        raise ValueError("Amount must be positive")
    # Process withdrawal

# Good: Use assertions for debugging
def calculate_average(numbers):
    assert len(numbers) > 0, "List should not be empty"  # Developer check
    return sum(numbers) / len(numbers)
```

### Exception Best Practices

```python
# 1. Be specific with exceptions
# Bad
try:
    value = int("hello")
except:  # Too broad
    pass

# Good
try:
    value = int("hello")
except ValueError:  # Specific
    pass

# 2. Don't silently ignore exceptions
# Bad
try:
    risky_operation()
except Exception:
    pass  # Silent failure - hard to debug

# Good
try:
    risky_operation()
except Exception as e:
    logger.error(f"Operation failed: {e}")
    # Or re-raise, or handle appropriately

# 3. Clean up resources
# Bad
f = open("file.txt")
data = f.read()
f.close()  # Might not execute if exception occurs

# Good
with open("file.txt") as f:
    data = f.read()

# 4. Provide context in exceptions
# Bad
raise ValueError()

# Good
raise ValueError(f"Invalid age: {age}. Must be between 0 and 150")

# 5. Use custom exceptions for domain logic
class InsufficientFundsError(Exception):
    pass

class BankAccount:
    def withdraw(self, amount):
        if amount > self.balance:
            raise InsufficientFundsError(
                f"Cannot withdraw ${amount}. Balance: ${self.balance}"
            )
```

### Practical Example: File Processing with Error Handling

```python
import json
import logging

# Setup logging
logging.basicConfig(level=logging.ERROR)
logger = logging.getLogger(__name__)

class FileProcessingError(Exception):
    """Base exception for file processing errors"""
    pass

class FileNotFoundError(FileProcessingError):
    """File does not exist"""
    pass

class InvalidDataError(FileProcessingError):
    """File contains invalid data"""
    pass

def process_json_file(filename):
    """
    Process JSON file with comprehensive error handling

    Returns:
        dict: Parsed JSON data

    Raises:
        FileProcessingError: If processing fails
    """
    try:
        # Try to open file
        with open(filename, 'r') as f:
            try:
                # Try to parse JSON
                data = json.load(f)
            except json.JSONDecodeError as e:
                logger.error(f"Invalid JSON in {filename}: {e}")
                raise InvalidDataError(f"File contains invalid JSON: {e}") from e

            # Validate data structure
            if not isinstance(data, dict):
                raise InvalidDataError("Expected JSON object, got array or primitive")

            # Validate required fields
            required_fields = ['name', 'age', 'email']
            missing = [field for field in required_fields if field not in data]
            if missing:
                raise InvalidDataError(f"Missing required fields: {missing}")

            return data

    except FileNotFoundError as e:
        logger.error(f"File {filename} not found")
        raise FileProcessingError(f"Cannot find file: {filename}") from e

    except PermissionError as e:
        logger.error(f"Permission denied for {filename}")
        raise FileProcessingError(f"Cannot access file: {filename}") from e

    except Exception as e:
        # Catch unexpected errors
        logger.error(f"Unexpected error processing {filename}: {e}")
        raise FileProcessingError(f"Failed to process file: {e}") from e

# Usage
def main():
    files = ["user1.json", "user2.json", "user3.json"]
    results = []
    errors = []

    for filename in files:
        try:
            data = process_json_file(filename)
            results.append(data)
            print(f"✓ Processed {filename}")
        except FileProcessingError as e:
            errors.append((filename, str(e)))
            print(f"✗ Failed to process {filename}: {e}")

    # Summary
    print(f"\nProcessed: {len(results)}/{len(files)}")
    if errors:
        print("\nErrors:")
        for filename, error in errors:
            print(f"  - {filename}: {error}")

    return results

# Example with retry logic
def fetch_with_retry(url, max_retries=3, backoff=1):
    """Fetch data with exponential backoff retry"""
    import time
    import random

    for attempt in range(max_retries):
        try:
            # Simulated network request
            response = fetch_data(url)
            return response

        except ConnectionError as e:
            if attempt == max_retries - 1:
                # Last attempt failed
                logger.error(f"Failed after {max_retries} attempts: {e}")
                raise

            # Wait with exponential backoff
            wait_time = backoff * (2 ** attempt) + random.uniform(0, 1)
            logger.warning(f"Attempt {attempt + 1} failed. Retrying in {wait_time:.2f}s...")
            time.sleep(wait_time)
```

### Interview Insight: Why This Matters

Understanding exception handling helps you:

- Write robust, fault-tolerant code
- Debug issues effectively
- Manage resources properly
- Design error hierarchies

Common interview questions from this section:

- "What's the difference between error and exception?"
- "Explain try-except-else-finally execution flow"
- "When would you create custom exceptions?"
- "What is EAFP vs LBYL? Which is more Pythonic?"
- "How do context managers work? Explain **enter** and **exit**"
- "What's the difference between `raise` and `raise from`?"
- "Why shouldn't you use bare `except:`?"
- "What are assertions and when should you use them?"

---

## Modules, Packages, and Virtual Environments

### Overview

Python's module system allows code organization and reuse. Understanding modules, packages, and virtual environments is essential for managing projects effectively.

### Modules

A module is a single Python file containing definitions and statements.

#### Creating Modules

```python
# File: math_utils.py
"""
Math utility functions
This is the module docstring
"""

PI = 3.14159

def add(a, b):
    """Add two numbers"""
    return a + b

def multiply(a, b):
    """Multiply two numbers"""
    return a * b

class Calculator:
    """Simple calculator class"""
    def __init__(self):
        self.result = 0

    def add(self, value):
        self.result += value
        return self.result

# Module-level code (runs when imported)
print("math_utils module loaded")

# Code that only runs when executed directly
if __name__ == "__main__":
    print("Running math_utils as main program")
    print(add(5, 3))
```

#### Importing Modules

```python
# 1. Import entire module
import math_utils
result = math_utils.add(5, 3)
print(math_utils.PI)
calc = math_utils.Calculator()

# 2. Import specific items
from math_utils import add, multiply
result = add(5, 3)  # No prefix needed

# 3. Import with alias
import math_utils as mu
result = mu.add(5, 3)

from math_utils import Calculator as Calc
calc = Calc()

# 4. Import everything (NOT recommended)
from math_utils import *  # Pollutes namespace
result = add(5, 3)

# 5. Import standard library modules
import os
import sys
import json
from pathlib import Path
from collections import defaultdict

# 6. Conditional imports
try:
    import numpy as np
    has_numpy = True
except ImportError:
    has_numpy = False
    print("NumPy not available")
```

#### Module Search Path

```python
# Python searches for modules in this order:
import sys

# 1. Current directory
# 2. PYTHONPATH environment variable
# 3. Standard library directories
# 4. Site-packages (third-party packages)

print(sys.path)  # List of directories Python searches

# Add directory to search path
sys.path.append('/path/to/my/modules')

# Module attributes
import math_utils
print(math_utils.__name__)      # 'math_utils'
print(math_utils.__file__)      # Path to module file
print(math_utils.__doc__)       # Module docstring
```

### Packages

A package is a directory containing Python modules and an `__init__.py` file.

#### Package Structure

```
mypackage/
    __init__.py          # Makes it a package
    module1.py
    module2.py
    subpackage/
        __init__.py
        submodule1.py
        submodule2.py
```

#### Creating Packages

```python
# File: mypackage/__init__.py
"""
MyPackage - A sample package
"""

# Package initialization code
print("Initializing mypackage")

# Define what gets imported with "from mypackage import *"
__all__ = ['module1', 'module2']

# Import commonly used items to package level
from .module1 import function1
from .module2 import function2

# Package version
__version__ = "1.0.0"

# File: mypackage/module1.py
def function1():
    return "Function 1 from module1"

class Class1:
    pass

# File: mypackage/module2.py
def function2():
    return "Function 2 from module2"

# File: mypackage/subpackage/__init__.py
from .submodule1 import subfunction1

# File: mypackage/subpackage/submodule1.py
def subfunction1():
    return "Function from submodule1"
```

#### Importing from Packages

```python
# 1. Import package
import mypackage
result = mypackage.function1()  # From __init__.py

# 2. Import module from package
import mypackage.module1
result = mypackage.module1.function1()

# Or with 'from'
from mypackage import module1
result = module1.function1()

# 3. Import specific items
from mypackage.module1 import function1, Class1
result = function1()

# 4. Import from subpackage
from mypackage.subpackage import submodule1
result = submodule1.subfunction1()

# Or directly
from mypackage.subpackage.submodule1 import subfunction1
result = subfunction1()

# 5. Relative imports (within package)
# File: mypackage/module2.py
from .module1 import function1       # Same package
from ..otherpackage import something # Parent package
from .subpackage import submodule1   # Subpackage
```

```mermaid
graph TD
    A[mypackage/] --> B[__init__.py]
    A --> C[module1.py]
    A --> D[module2.py]
    A --> E[subpackage/]

    E --> F[__init__.py]
    E --> G[submodule1.py]
    E --> H[submodule2.py]

    style A fill:#FFE6A0
    style E fill:#E6F3FF
    style B fill:#90EE90
    style F fill:#90EE90
```

### The `__name__` and `__main__` Pattern

```python
# File: script.py
def main():
    """Main program logic"""
    print("Running main program")

# Module-level code
print(f"__name__ is: {__name__}")

# Only runs when executed directly (not when imported)
if __name__ == "__main__":
    main()

# When you run: python script.py
# Output:
# __name__ is: __main__
# Running main program

# When you import: import script
# Output:
# __name__ is: script
# (main() doesn't run)
```

### Virtual Environments

Virtual environments isolate project dependencies.

#### Creating Virtual Environments

```bash
# Using venv (built-in, Python 3.3+)
python -m venv myenv

# Activate virtual environment
# Windows:
myenv\Scripts\activate

# macOS/Linux:
source myenv/bin/activate

# Deactivate
deactivate

# Using virtualenv (third-party, more features)
pip install virtualenv
virtualenv myenv

# Using conda (Anaconda)
conda create -n myenv python=3.10
conda activate myenv
conda deactivate
```

#### Why Use Virtual Environments?

```python
# Problem without virtual environments:
# Project A needs Django 3.0
# Project B needs Django 4.0
# System-wide installation creates conflicts!

# Solution: Each project has its own environment
# ProjectA/myenv/ has Django 3.0
# ProjectB/myenv/ has Django 4.0
# No conflicts!
```

```mermaid
graph TD
    A[System Python] --> B[Global Site-Packages]
    A --> C[Project A venv]
    A --> D[Project B venv]

    C --> C1[Django 3.0]
    C --> C2[numpy 1.20]

    D --> D1[Django 4.0]
    D --> D2[numpy 1.23]

    style A fill:#FFB6C6
    style C fill:#E6F3FF
    style D fill:#E6F3FF
    style B fill:#FFE6A0
```

### Package Management with pip

```bash
# Install package
pip install requests

# Install specific version
pip install requests==2.28.0

# Install minimum version
pip install requests>=2.25.0

# Install from requirements file
pip install -r requirements.txt

# Uninstall package
pip uninstall requests

# List installed packages
pip list

# Show package details
pip show requests

# Search for packages (deprecated in newer pip versions)
pip search django

# Freeze current environment
pip freeze > requirements.txt

# Upgrade package
pip install --upgrade requests

# Install in editable mode (for development)
pip install -e .

# Install from git repository
pip install git+https://github.com/user/repo.git
```

#### requirements.txt

```
# requirements.txt
# Exact versions (reproducible)
requests==2.28.0
numpy==1.24.0
pandas==1.5.2

# Minimum version
Django>=4.0

# Version range
pytest>=7.0,<8.0

# With extras
requests[security]

# From GitHub
git+https://github.com/user/repo.git@v1.0

# Local package
-e ./my_local_package

# Include another requirements file
-r requirements-dev.txt
```

### Importing Best Practices

```python
# 1. Import order (PEP 8)
# Standard library imports
import os
import sys
from pathlib import Path

# Third-party imports
import numpy as np
import pandas as pd
import requests

# Local application imports
from myapp import config
from myapp.utils import helper_function

# 2. Avoid circular imports
# File: a.py
import b  # Imports b

def function_a():
    b.function_b()

# File: b.py
import a  # Circular! Imports a

def function_b():
    a.function_a()  # This will cause problems

# Solution: Import inside function, or restructure
# File: b.py
def function_b():
    import a  # Import here, not at module level
    a.function_a()

# 3. Lazy imports (for optional dependencies)
def plot_data(data):
    try:
        import matplotlib.pyplot as plt
    except ImportError:
        raise ImportError("matplotlib required for plotting")

    plt.plot(data)
    plt.show()

# 4. Use absolute imports over relative
# Good
from mypackage.module import function

# Less clear (relative)
from .module import function

# 5. Don't use import * except in __init__.py
# Bad
from module import *

# Good
from module import function1, function2

# Or
import module
```

### Creating Installable Packages

```python
# Directory structure
myproject/
    mypackage/
        __init__.py
        module1.py
        module2.py
    tests/
        __init__.py
        test_module1.py
    setup.py
    README.md
    LICENSE
    requirements.txt

# setup.py (traditional)
from setuptools import setup, find_packages

setup(
    name="mypackage",
    version="1.0.0",
    author="Your Name",
    author_email="you@example.com",
    description="A sample package",
    long_description=open("README.md").read(),
    long_description_content_type="text/markdown",
    url="https://github.com/yourusername/mypackage",
    packages=find_packages(),
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires=">=3.7",
    install_requires=[
        "requests>=2.25.0",
        "numpy>=1.20.0",
    ],
    extras_require={
        "dev": ["pytest", "black", "mypy"],
    },
)

# Install in development mode
# pip install -e .

# Build distribution
# python setup.py sdist bdist_wheel

# Upload to PyPI
# pip install twine
# twine upload dist/*

# pyproject.toml (modern, PEP 518)
[build-system]
requires = ["setuptools>=45", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "mypackage"
version = "1.0.0"
description = "A sample package"
requires-python = ">=3.7"
dependencies = [
    "requests>=2.25.0",
    "numpy>=1.20.0",
]

[project.optional-dependencies]
dev = ["pytest", "black", "mypy"]
```

### Practical Example: Project Structure

```python
# Real-world project structure
myproject/
    ├── myapp/                 # Main package
    │   ├── __init__.py
    │   ├── config.py         # Configuration
    │   ├── models.py         # Data models
    │   ├── views.py          # Business logic
    │   ├── utils/            # Utilities subpackage
    │   │   ├── __init__.py
    │   │   ├── helpers.py
    │   │   └── validators.py
    │   └── api/              # API subpackage
    │       ├── __init__.py
    │       ├── routes.py
    │       └── handlers.py
    ├── tests/                # Test suite
    │   ├── __init__.py
    │   ├── test_models.py
    │   ├── test_views.py
    │   └── fixtures/
    ├── docs/                 # Documentation
    │   ├── conf.py
    │   └── index.md
    ├── scripts/              # Utility scripts
    │   └── setup_db.py
    ├── .env                  # Environment variables
    ├── .gitignore
    ├── requirements.txt      # Production dependencies
    ├── requirements-dev.txt  # Development dependencies
    ├── setup.py              # Package setup
    ├── README.md
    ├── LICENSE
    └── pyproject.toml

# File: myapp/__init__.py
"""
MyApp - A sample application
"""
from .config import Config
from .models import User, Product

__version__ = "1.0.0"
__all__ = ["Config", "User", "Product"]

# File: myapp/config.py
import os
from pathlib import Path

class Config:
    BASE_DIR = Path(__file__).parent.parent
    DEBUG = os.getenv("DEBUG", "False") == "True"
    DATABASE_URL = os.getenv("DATABASE_URL", "sqlite:///app.db")

# File: myapp/models.py
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price

# File: myapp/utils/helpers.py
def format_currency(amount):
    return f"${amount:.2f}"

# File: tests/test_models.py
import unittest
from myapp.models import User

class TestUser(unittest.TestCase):
    def test_user_creation(self):
        user = User("Alice", "alice@example.com")
        self.assertEqual(user.name, "Alice")

# File: main.py (entry point)
from myapp import Config, User
from myapp.utils.helpers import format_currency

def main():
    config = Config()
    user = User("Bob", "bob@example.com")
    print(f"User: {user.name}")

if __name__ == "__main__":
    main()
```

### Interview Insight: Why This Matters

Understanding modules and packages helps you:

- Organize code effectively
- Manage dependencies
- Create reusable libraries
- Avoid import conflicts

Common interview questions from this section:

- "What's the difference between a module and a package?"
- "Explain the purpose of **init**.py"
- "What does `if __name__ == '__main__':` do?"
- "How does Python's module search path work?"
- "What are relative vs absolute imports?"
- "Why use virtual environments?"
- "Explain the difference between pip install and pip install -e"
- "How do you create an installable Python package?"

---

## Standard Library Essentials

### Overview

Python's standard library is extensive, providing modules for common tasks without requiring external packages. Mastering key standard library modules is essential for efficient Python development.

### File and Directory Operations

#### os Module

```python
import os

# Current working directory
cwd = os.getcwd()
print(cwd)

# Change directory
os.chdir('/path/to/directory')

# List directory contents
files = os.listdir('.')
print(files)

# Create directory
os.mkdir('new_directory')
os.makedirs('path/to/nested/directory')  # Create intermediate directories

# Remove directory
os.rmdir('directory')  # Only if empty
os.removedirs('path/to/nested')  # Remove empty nested directories

# Remove file
os.remove('file.txt')

# Rename/move
os.rename('old_name.txt', 'new_name.txt')

# Check if exists
exists = os.path.exists('file.txt')
is_file = os.path.isfile('file.txt')
is_dir = os.path.isdir('directory')

# Path operations
path = os.path.join('directory', 'subdirectory', 'file.txt')
dirname = os.path.dirname(path)
basename = os.path.basename(path)
name, ext = os.path.splitext('file.txt')  # ('file', '.txt')

# Absolute path
abs_path = os.path.abspath('relative/path')

# Environment variables
home = os.environ.get('HOME')
os.environ['MY_VAR'] = 'value'

# Execute system command
exit_code = os.system('ls -l')

# Get file stats
stats = os.stat('file.txt')
print(stats.st_size)  # Size in bytes
print(stats.st_mtime)  # Last modified time
```

#### pathlib Module (Modern, OOP approach)

```python
from pathlib import Path

# Create Path object
p = Path('directory/file.txt')
p = Path.home() / 'documents' / 'file.txt'  # Home directory

# Current working directory
cwd = Path.cwd()

# Check existence
if p.exists():
    print("File exists")

if p.is_file():
    print("It's a file")

if p.is_dir():
    print("It's a directory")

# Read/write files
content = p.read_text()  # Read entire file
p.write_text("Hello, World!")  # Write to file

# Binary read/write
data = p.read_bytes()
p.write_bytes(b'\x00\x01\x02')

# Path properties
print(p.name)        # 'file.txt'
print(p.stem)        # 'file'
print(p.suffix)      # '.txt'
print(p.parent)      # Path('directory')
print(p.parts)       # ('directory', 'file.txt')

# Create directory
p.mkdir(parents=True, exist_ok=True)

# List directory contents
for item in Path('.').iterdir():
    print(item)

# Glob patterns
for txt_file in Path('.').glob('*.txt'):
    print(txt_file)

# Recursive glob
for py_file in Path('.').rglob('*.py'):
    print(py_file)

# Rename/move
p.rename('new_name.txt')

# Delete
p.unlink()  # Remove file
p.rmdir()   # Remove empty directory

# File stats
stats = p.stat()
print(f"Size: {stats.st_size} bytes")
```

#### shutil Module (High-level file operations)

```python
import shutil

# Copy file
shutil.copy('source.txt', 'destination.txt')
shutil.copy2('source.txt', 'dest.txt')  # Preserve metadata

# Copy directory tree
shutil.copytree('source_dir', 'dest_dir')

# Remove directory tree
shutil.rmtree('directory')

# Move file or directory
shutil.move('source', 'destination')

# Disk usage
usage = shutil.disk_usage('/')
print(f"Total: {usage.total / (1024**3):.2f} GB")
print(f"Used: {usage.used / (1024**3):.2f} GB")
print(f"Free: {usage.free / (1024**3):.2f} GB")

# Archive operations
shutil.make_archive('backup', 'zip', 'directory_to_backup')
shutil.unpack_archive('backup.zip', 'extract_to')

# Find executable
python_path = shutil.which('python')
```

### Data Serialization

#### json Module

```python
import json

# Python object to JSON string
data = {
    'name': 'Alice',
    'age': 30,
    'hobbies': ['reading', 'coding'],
    'active': True
}

json_string = json.dumps(data)
print(json_string)

# With formatting
json_string = json.dumps(data, indent=4, sort_keys=True)

# JSON string to Python object
parsed = json.loads(json_string)

# Write to file
with open('data.json', 'w') as f:
    json.dump(data, f, indent=4)

# Read from file
with open('data.json', 'r') as f:
    data = json.load(f)

# Custom serialization
from datetime import datetime

class DateTimeEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        return super().default(obj)

data = {'timestamp': datetime.now()}
json_string = json.dumps(data, cls=DateTimeEncoder)
```

#### pickle Module (Python-specific serialization)

```python
import pickle

# Serialize complex Python objects
data = {
    'list': [1, 2, 3],
    'dict': {'key': 'value'},
    'function': lambda x: x * 2
}

# Save to file
with open('data.pkl', 'wb') as f:
    pickle.dump(data, f)

# Load from file
with open('data.pkl', 'rb') as f:
    loaded_data = pickle.load(f)

# Serialize to bytes
bytes_data = pickle.dumps(data)
restored = pickle.loads(bytes_data)

# Warning: Only unpickle data from trusted sources!
# pickle can execute arbitrary code
```

#### csv Module

```python
import csv

# Write CSV
data = [
    ['Name', 'Age', 'City'],
    ['Alice', 30, 'NYC'],
    ['Bob', 25, 'LA'],
    ['Charlie', 35, 'Chicago']
]

with open('data.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerows(data)

# Read CSV
with open('data.csv', 'r') as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)

# DictReader/DictWriter
with open('data.csv', 'w', newline='') as f:
    fieldnames = ['Name', 'Age', 'City']
    writer = csv.DictWriter(f, fieldnames=fieldnames)

    writer.writeheader()
    writer.writerow({'Name': 'Alice', 'Age': 30, 'City': 'NYC'})
    writer.writerow({'Name': 'Bob', 'Age': 25, 'City': 'LA'})

with open('data.csv', 'r') as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row['Name'], row['Age'])
```

### Date and Time

#### datetime Module

```python
from datetime import datetime, date, time, timedelta

# Current date and time
now = datetime.now()
print(now)  # 2026-05-15 14:30:00.123456

today = date.today()
print(today)  # 2026-05-15

# Create specific datetime
dt = datetime(2026, 5, 15, 14, 30, 0)

# Parse string to datetime
dt = datetime.strptime('2026-05-15 14:30', '%Y-%m-%d %H:%M')

# Format datetime to string
formatted = dt.strftime('%B %d, %Y at %I:%M %p')
print(formatted)  # May 15, 2026 at 02:30 PM

# Date/time components
print(dt.year, dt.month, dt.day)
print(dt.hour, dt.minute, dt.second)

# Timedelta (duration)
delta = timedelta(days=7, hours=3, minutes=30)
future = now + delta
past = now - delta

# Difference between dates
diff = date(2026, 12, 31) - date.today()
print(f"Days until end of year: {diff.days}")

# Compare dates
if date.today() > date(2025, 1, 1):
    print("We're past 2025!")

# ISO format
iso = dt.isoformat()  # '2026-05-15T14:30:00'
dt = datetime.fromisoformat(iso)

# Timestamp (Unix epoch)
timestamp = dt.timestamp()
dt = datetime.fromtimestamp(timestamp)
```

#### time Module

```python
import time

# Current time (seconds since epoch)
now = time.time()
print(now)

# Sleep (pause execution)
time.sleep(1)  # Sleep for 1 second
time.sleep(0.1)  # Sleep for 100ms

# Measure execution time
start = time.time()
# ... code to measure ...
end = time.time()
elapsed = end - start
print(f"Execution time: {elapsed:.4f} seconds")

# Performance counter (higher resolution)
start = time.perf_counter()
# ... code ...
end = time.perf_counter()
elapsed = end - start

# Format time
local_time = time.localtime()
formatted = time.strftime('%Y-%m-%d %H:%M:%S', local_time)
```

### Collections

#### collections Module

```python
from collections import (
    Counter, defaultdict, OrderedDict,
    deque, namedtuple, ChainMap
)

# Counter - count hashable objects
text = "hello world"
counter = Counter(text)
print(counter)  # Counter({'l': 3, 'o': 2, 'h': 1, ...})
print(counter.most_common(2))  # [('l', 3), ('o', 2)]

# defaultdict - dict with default values
dd = defaultdict(int)
for char in "hello":
    dd[char] += 1
print(dd)  # defaultdict(<class 'int'>, {'h': 1, 'e': 1, 'l': 2, 'o': 1})

dd = defaultdict(list)
dd['key'].append(1)  # No KeyError!

# deque - double-ended queue (efficient appends/pops from both ends)
dq = deque([1, 2, 3])
dq.append(4)        # Add to right
dq.appendleft(0)    # Add to left
dq.pop()            # Remove from right
dq.popleft()        # Remove from left
dq.rotate(1)        # Rotate right
dq.rotate(-1)       # Rotate left

# namedtuple - tuple with named fields
Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
print(p.x, p.y)  # 10 20
print(p[0], p[1])  # 10 20 (still works like tuple)

# ChainMap - combine multiple dicts
dict1 = {'a': 1, 'b': 2}
dict2 = {'b': 3, 'c': 4}
chain = ChainMap(dict1, dict2)
print(chain['a'])  # 1
print(chain['b'])  # 2 (from dict1, first dict takes precedence)
print(chain['c'])  # 4
```

### Itertools

```python
import itertools

# count - infinite counter
for i in itertools.count(10, 2):  # Start at 10, step 2
    if i > 20:
        break
    print(i)  # 10, 12, 14, 16, 18, 20

# cycle - infinite cycle through iterable
counter = 0
for item in itertools.cycle(['A', 'B', 'C']):
    if counter >= 7:
        break
    print(item)  # A, B, C, A, B, C, A
    counter += 1

# repeat - repeat element
for x in itertools.repeat('Hello', 3):
    print(x)  # Hello, Hello, Hello

# chain - chain iterables
combined = itertools.chain([1, 2], [3, 4], [5, 6])
print(list(combined))  # [1, 2, 3, 4, 5, 6]

# combinations - all combinations of length r
print(list(itertools.combinations([1, 2, 3], 2)))
# [(1, 2), (1, 3), (2, 3)]

# permutations - all permutations of length r
print(list(itertools.permutations([1, 2, 3], 2)))
# [(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)]

# product - Cartesian product
print(list(itertools.product([1, 2], ['A', 'B'])))
# [(1, 'A'), (1, 'B'), (2, 'A'), (2, 'B')]

# groupby - group consecutive elements
data = [('A', 1), ('A', 2), ('B', 3), ('B', 4), ('A', 5)]
for key, group in itertools.groupby(data, lambda x: x[0]):
    print(key, list(group))
# A [('A', 1), ('A', 2)]
# B [('B', 3), ('B', 4)]
# A [('A', 5)]

# islice - slice iterator
print(list(itertools.islice(range(10), 2, 7)))  # [2, 3, 4, 5, 6]

# takewhile/dropwhile
print(list(itertools.takewhile(lambda x: x < 5, [1, 2, 3, 6, 4, 1])))
# [1, 2, 3]
```

### Regular Expressions

```python
import re

text = "Contact us at support@example.com or sales@example.com"

# Search for pattern
match = re.search(r'\w+@\w+\.\w+', text)
if match:
    print(match.group())  # support@example.com

# Find all matches
emails = re.findall(r'\w+@\w+\.\w+', text)
print(emails)  # ['support@example.com', 'sales@example.com']

# Match from start
match = re.match(r'Contact', text)
if match:
    print("Starts with 'Contact'")

# Substitute
new_text = re.sub(r'\w+@\w+\.\w+', '[EMAIL]', text)
print(new_text)  # Contact us at [EMAIL] or [EMAIL]

# Split
parts = re.split(r'\s+', "Split   by    whitespace")
print(parts)  # ['Split', 'by', 'whitespace']

# Compile pattern (for reuse)
pattern = re.compile(r'\w+@\w+\.\w+')
emails = pattern.findall(text)

# Groups
text = "John Doe, 30 years old"
match = re.search(r'(\w+) (\w+), (\d+)', text)
if match:
    print(match.group(1))  # John
    print(match.group(2))  # Doe
    print(match.group(3))  # 30
    print(match.groups())  # ('John', 'Doe', '30')

# Named groups
match = re.search(r'(?P<first>\w+) (?P<last>\w+)', text)
if match:
    print(match.group('first'))  # John
    print(match.groupdict())     # {'first': 'John', 'last': 'Doe'}

# Flags
match = re.search(r'HELLO', 'hello', re.IGNORECASE)
match = re.search(r'^line1.*line2', 'line1\nline2', re.MULTILINE | re.DOTALL)
```

### Random Numbers

```python
import random

# Random integer
n = random.randint(1, 10)  # 1 to 10 inclusive

# Random float [0.0, 1.0)
f = random.random()

# Random float in range
f = random.uniform(1.0, 10.0)

# Random choice
color = random.choice(['red', 'green', 'blue'])

# Random sample (without replacement)
sample = random.sample([1, 2, 3, 4, 5], 3)  # e.g., [2, 5, 1]

# Shuffle list in place
items = [1, 2, 3, 4, 5]
random.shuffle(items)

# Random choices (with replacement)
choices = random.choices([1, 2, 3], weights=[1, 2, 3], k=5)

# Set seed for reproducibility
random.seed(42)
print(random.random())  # Always same value with seed 42
```

### Practical Example: Log File Analyzer

```python
import re
from collections import Counter, defaultdict
from datetime import datetime
from pathlib import Path

class LogAnalyzer:
    """Analyze log files using standard library"""

    def __init__(self, log_file):
        self.log_file = Path(log_file)
        self.entries = []
        self.error_pattern = re.compile(
            r'(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) - (\w+) - (.+)'
        )

    def parse(self):
        """Parse log file"""
        with open(self.log_file, 'r') as f:
            for line in f:
                match = self.error_pattern.match(line.strip())
                if match:
                    timestamp, level, message = match.groups()
                    dt = datetime.strptime(timestamp, '%Y-%m-%d %H:%M:%S')
                    self.entries.append({
                        'timestamp': dt,
                        'level': level,
                        'message': message
                    })

    def count_by_level(self):
        """Count entries by log level"""
        levels = [entry['level'] for entry in self.entries]
        return Counter(levels)

    def errors_by_hour(self):
        """Group errors by hour"""
        by_hour = defaultdict(int)
        for entry in self.entries:
            if entry['level'] == 'ERROR':
                hour = entry['timestamp'].hour
                by_hour[hour] += 1
        return dict(sorted(by_hour.items()))

    def most_common_errors(self, n=5):
        """Find most common error messages"""
        errors = [
            entry['message'] for entry in self.entries
            if entry['level'] == 'ERROR'
        ]
        return Counter(errors).most_common(n)

    def summary(self):
        """Generate summary report"""
        print("=" * 50)
        print("LOG ANALYSIS SUMMARY")
        print("=" * 50)

        print(f"\nTotal entries: {len(self.entries)}")

        print("\nEntries by level:")
        for level, count in self.count_by_level().most_common():
            print(f"  {level}: {count}")

        print("\nErrors by hour:")
        for hour, count in self.errors_by_hour().items():
            print(f"  {hour:02d}:00 - {count} errors")

        print("\nMost common errors:")
        for message, count in self.most_common_errors(3):
            print(f"  [{count}x] {message[:50]}...")

# Usage
# analyzer = LogAnalyzer('app.log')
# analyzer.parse()
# analyzer.summary()
```

### Interview Insight: Why This Matters

Understanding the standard library helps you:

- Avoid reinventing the wheel
- Write efficient, idiomatic code
- Leverage battle-tested modules
- Minimize external dependencies

Common interview questions from this section:

- "What's the difference between os.path and pathlib?"
- "How do you serialize Python objects?"
- "Explain the difference between json and pickle"
- "What are collections.defaultdict and collections.Counter?"
- "How do you work with dates and times in Python?"
- "What's the difference between time.time() and time.perf_counter()?"
- "Explain itertools.chain and itertools.groupby"
- "How do you use regular expressions in Python?"

---

## Memory Management and References

### Overview

Understanding how Python manages memory is crucial for writing efficient code and avoiding memory-related bugs. Python uses automatic memory management with reference counting and garbage collection.

### Python Memory Model

#### Object Identity and References

```python
# Every object has: id (identity), type, and value
x = 42
print(id(x))      # Memory address (identity)
print(type(x))    # <class 'int'>
print(x)          # Value: 42

# Variables are references (names bound to objects)
a = [1, 2, 3]
b = a  # b references same object as a

b.append(4)
print(a)  # [1, 2, 3, 4] - both point to same list!

# Check if same object
print(a is b)      # True (same identity)
print(id(a) == id(b))  # True

# Different objects with same value
x = [1, 2, 3]
y = [1, 2, 3]
print(x == y)  # True (equal values)
print(x is y)  # False (different objects)
```

```mermaid
graph LR
    A[Variable 'a'] --> C[List Object<br/>[1, 2, 3]]
    B[Variable 'b'] --> C

    D[Variable 'x'] --> E[List Object<br/>[1, 2, 3]]
    F[Variable 'y'] --> G[List Object<br/>[1, 2, 3]]

    style C fill:#90EE90
    style E fill:#FFE6A0
    style G fill:#E6F3FF
```

#### `is` vs `==`

```python
# == compares values
# is compares identity

a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)  # True (same value)
print(a is b)  # False (different objects)

# Small integers are cached (interned)
x = 256
y = 256
print(x is y)  # True (same object! Python caches small ints)

x = 257
y = 257
print(x is y)  # False (larger ints may not be cached)

# String interning
s1 = "hello"
s2 = "hello"
print(s1 is s2)  # True (strings are interned)

s1 = "hello world"
s2 = "hello world"
print(s1 is s2)  # May be True or False (depends on string)

# Always use 'is' for None, True, False
x = None
if x is None:  # Correct
    pass

if x == None:  # Works but not idiomatic
    pass
```

### Reference Counting

Python's primary memory management mechanism.

```python
import sys

# Reference count
x = [1, 2, 3]
print(sys.getrefcount(x))  # At least 2 (x and function parameter)

y = x  # Another reference
print(sys.getrefcount(x))  # Increased

del y  # Remove reference
print(sys.getrefcount(x))  # Decreased

# When reference count reaches 0, object is deallocated

# Example: reference count lifecycle
data = [1, 2, 3]  # refcount = 1
refs = [data]     # refcount = 2
more_refs = refs  # data refcount = 3 (via refs and more_refs)

del data          # refcount = 2
refs.clear()      # refcount = 1
more_refs = None  # refcount = 0 → object deallocated
```

### Mutable vs Immutable Objects

#### Immutable Types

Cannot be changed after creation. Operations create new objects.

```python
# Immutable: int, float, complex, bool, str, tuple, frozenset, bytes

# Strings are immutable
s = "hello"
print(id(s))
s = s + " world"  # Creates NEW string object
print(id(s))  # Different id

# Integers are immutable
x = 10
print(id(x))
x += 5  # Creates new int object
print(id(x))  # Different id

# Tuples are immutable
t = (1, 2, 3)
# t[0] = 10  # TypeError: tuple does not support item assignment

# But if tuple contains mutable objects...
t = ([1, 2], [3, 4])
t[0].append(3)  # This works! The list inside is mutable
print(t)  # ([1, 2, 3], [3, 4])
```

#### Mutable Types

Can be modified in place without creating new object.

```python
# Mutable: list, dict, set, bytearray, user-defined classes

# Lists are mutable
lst = [1, 2, 3]
print(id(lst))
lst.append(4)  # Modifies same object
print(id(lst))  # Same id!

# Dictionaries are mutable
d = {'a': 1}
print(id(d))
d['b'] = 2  # Modifies same object
print(id(d))  # Same id

# Sets are mutable
s = {1, 2, 3}
print(id(s))
s.add(4)  # Modifies same object
print(id(s))  # Same id
```

### Shallow vs Deep Copy

```python
import copy

# Assignment - both reference same object
original = [1, 2, [3, 4]]
reference = original

reference.append(5)
print(original)  # [1, 2, [3, 4], 5] - modified!

# Shallow copy - new object, but nested objects are references
original = [1, 2, [3, 4]]
shallow = original.copy()  # or copy.copy(original) or original[:]

shallow.append(5)
print(original)  # [1, 2, [3, 4]] - not modified
print(shallow)   # [1, 2, [3, 4], 5]

# But nested objects are still shared!
shallow[2].append(5)
print(original)  # [1, 2, [3, 4, 5]] - nested list modified!
print(shallow)   # [1, 2, [3, 4, 5]]

# Deep copy - new object with copies of nested objects
original = [1, 2, [3, 4]]
deep = copy.deepcopy(original)

deep[2].append(5)
print(original)  # [1, 2, [3, 4]] - not modified
print(deep)      # [1, 2, [3, 4, 5]]

# Dictionary copying
d1 = {'a': 1, 'b': [2, 3]}
d2 = d1.copy()  # Shallow copy
d2['b'].append(4)
print(d1)  # {'a': 1, 'b': [2, 3, 4]} - nested list shared!

d3 = copy.deepcopy(d1)
d3['b'].append(5)
print(d1)  # {'a': 1, 'b': [2, 3, 4]} - not affected
```

```mermaid
graph TD
    subgraph "Assignment"
        A1[var1] --> O1[Object]
        A2[var2] --> O1
    end

    subgraph "Shallow Copy"
        B1[original] --> O2[List Object]
        B2[shallow] --> O3[New List Object]
        O2 --> N1[Nested Object]
        O3 --> N1
    end

    subgraph "Deep Copy"
        C1[original] --> O4[List Object]
        C2[deep] --> O5[New List Object]
        O4 --> N2[Nested Object]
        O5 --> N3[Copy of Nested]
    end

    style O1 fill:#FFB6C6
    style N1 fill:#FFE6A0
    style N2 fill:#E6F3FF
    style N3 fill:#90EE90
```

### Garbage Collection

Python uses garbage collection to handle circular references.

```python
import gc

# Garbage collector info
print(gc.isenabled())  # True (enabled by default)
print(gc.get_count())  # (threshold0, threshold1, threshold2)

# Circular reference (reference counting can't handle this)
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None

# Create circular reference
node1 = Node(1)
node2 = Node(2)
node1.next = node2
node2.next = node1  # Circular!

# Even after deleting variables, objects won't be freed by refcount alone
del node1, node2

# Garbage collector detects and cleans up circular references
gc.collect()  # Manually trigger collection

# Disable/enable garbage collection
gc.disable()
gc.enable()

# Garbage collector generations (0, 1, 2)
# Generation 0: youngest objects (collected most frequently)
# Generation 1: survived one collection
# Generation 2: survived two collections (collected least frequently)

# Set thresholds
gc.set_threshold(700, 10, 10)  # (gen0, gen1, gen2)

# Get all tracked objects
# objects = gc.get_objects()  # All objects tracked by GC

# Debugging memory leaks
gc.set_debug(gc.DEBUG_LEAK)
```

### Memory Optimization Techniques

#### 1. Use Generators for Large Datasets

```python
# Bad: Creates entire list in memory
def get_numbers(n):
    return [i for i in range(n)]

numbers = get_numbers(1000000)  # ~8MB in memory

# Good: Generator - one item at a time
def get_numbers(n):
    for i in range(n):
        yield i

numbers = get_numbers(1000000)  # Minimal memory
for num in numbers:
    pass  # Process one at a time
```

#### 2. Use `__slots__` for Many Instances

```python
# Without __slots__: each instance has __dict__
class PointSlow:
    def __init__(self, x, y):
        self.x = x
        self.y = y

# With __slots__: fixed attributes, no __dict__
class PointFast:
    __slots__ = ['x', 'y']

    def __init__(self, x, y):
        self.x = x
        self.y = y

# Memory usage
import sys
p1 = PointSlow(1, 2)
p2 = PointFast(1, 2)

print(sys.getsizeof(p1.__dict__))  # ~232 bytes
# print(sys.getsizeof(p2.__dict__))  # AttributeError - no __dict__!

# Creating many instances
# PointFast uses ~40% less memory than PointSlow

# Trade-off: can't add new attributes dynamically
p1.z = 3  # Works
# p2.z = 3  # AttributeError!
```

#### 3. Use Appropriate Data Structures

```python
# Memory efficient choices

# Instead of list, use array for homogeneous numeric data
import array
arr = array.array('i', [1, 2, 3, 4, 5])  # Less memory than list

# Use tuple instead of list for immutable data
coordinates = (10, 20)  # Less memory than [10, 20]

# Use set for membership testing (also faster)
# O(1) vs O(n) for list

# Use deque for queue operations
from collections import deque
queue = deque()  # More efficient than list for append/pop at both ends

# Use generators instead of list comprehensions when possible
# Good for large data
squares = (x**2 for x in range(1000000))  # Generator
# Instead of:
# squares = [x**2 for x in range(1000000)]  # List
```

#### 4. Delete Unused Objects

```python
# Explicitly delete large objects when done
large_data = [i for i in range(10000000)]
# ... use data ...
del large_data  # Free memory immediately

# Or use context managers
class ResourceManager:
    def __enter__(self):
        self.resource = allocate_large_resource()
        return self.resource

    def __exit__(self, exc_type, exc_val, exc_tb):
        del self.resource  # Clean up
        return False

with ResourceManager() as resource:
    # Use resource
    pass
# Resource automatically cleaned up
```

### Weak References

References that don't increase reference count.

```python
import weakref

class ExpensiveObject:
    def __init__(self, name):
        self.name = name

    def __del__(self):
        print(f"{self.name} being deleted")

# Regular reference
obj = ExpensiveObject("regular")
# Object stays alive

# Weak reference
obj = ExpensiveObject("weak")
weak_ref = weakref.ref(obj)

print(weak_ref())  # Access object: <ExpensiveObject...>
print(weak_ref().name)  # "weak"

del obj  # Object can be garbage collected
# "weak being deleted" printed

print(weak_ref())  # None (object was deleted)

# WeakValueDictionary - values don't prevent GC
cache = weakref.WeakValueDictionary()

obj = ExpensiveObject("cached")
cache['key'] = obj

print(cache['key'])  # Works

del obj  # Object deleted from cache automatically
# print(cache['key'])  # KeyError

# Use case: caching without preventing GC
```

### Memory Profiling

```python
# 1. sys.getsizeof - get size of object
import sys

x = [1, 2, 3, 4, 5]
print(sys.getsizeof(x))  # Size in bytes

# Note: doesn't include size of referenced objects!
nested = [[1, 2], [3, 4]]
print(sys.getsizeof(nested))  # Only outer list size

# 2. tracemalloc - trace memory allocations
import tracemalloc

tracemalloc.start()

# Code to profile
data = [i for i in range(100000)]

current, peak = tracemalloc.get_traced_memory()
print(f"Current memory: {current / 1024:.2f} KB")
print(f"Peak memory: {peak / 1024:.2f} KB")

tracemalloc.stop()

# 3. Get top memory consumers
tracemalloc.start()

# ... code ...

snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')

print("Top 3 memory allocations:")
for stat in top_stats[:3]:
    print(stat)

tracemalloc.stop()

# 4. External tools
# memory_profiler: line-by-line memory usage
# @profile decorator
# python -m memory_profiler script.py

# objgraph: visualize object references
# import objgraph
# objgraph.show_most_common_types()
```

### Practical Example: Memory-Efficient Data Processing

```python
import sys
from collections import deque
import weakref

class MemoryEfficientProcessor:
    """Process large datasets efficiently"""

    def __init__(self, max_cache_size=1000):
        self.max_cache_size = max_cache_size
        self._cache = {}  # LRU cache
        self._cache_order = deque()

    def process_file_generator(self, filename):
        """Process file line by line (generator)"""
        with open(filename, 'r') as f:
            for line in f:
                # Process one line at a time
                yield self.process_line(line.strip())
                # Previous lines can be garbage collected

    def process_line(self, line):
        """Process single line with caching"""
        # Check cache
        if line in self._cache:
            return self._cache[line]

        # Process (expensive operation)
        result = line.upper()  # Simplified

        # Add to cache with LRU eviction
        if len(self._cache) >= self.max_cache_size:
            # Remove oldest entry
            oldest = self._cache_order.popleft()
            del self._cache[oldest]

        self._cache[line] = result
        self._cache_order.append(line)

        return result

    def batch_process(self, items, batch_size=1000):
        """Process in batches to limit memory"""
        results = []

        for i in range(0, len(items), batch_size):
            batch = items[i:i + batch_size]

            # Process batch
            batch_results = [self.process_line(item) for item in batch]
            results.extend(batch_results)

            # Clear intermediate data
            del batch, batch_results

        return results

# Compare memory usage
def inefficient_processing(filename):
    """Loads entire file into memory"""
    with open(filename, 'r') as f:
        lines = f.readlines()  # All lines in memory!

    return [line.strip().upper() for line in lines]

def efficient_processing(filename):
    """Processes file line by line"""
    processor = MemoryEfficientProcessor()

    # Generator - one line at a time
    for result in processor.process_file_generator(filename):
        # Process result
        pass  # or write to output

# Memory optimization with __slots__
class DataPoint:
    __slots__ = ['x', 'y', 'timestamp']

    def __init__(self, x, y, timestamp):
        self.x = x
        self.y = y
        self.timestamp = timestamp

# Creating 1 million data points
# With __slots__: ~60MB
# Without __slots__: ~150MB (2.5x more!)
```

### Interview Insight: Why This Matters

Understanding memory management helps you:

- Write memory-efficient code
- Debug memory leaks
- Optimize performance
- Make informed design decisions

Common interview questions from this section:

- "Explain reference counting in Python"
- "What's the difference between `is` and `==`?"
- "What are mutable vs immutable types?"
- "Explain shallow copy vs deep copy"
- "How does Python's garbage collector work?"
- "What are circular references and how are they handled?"
- "What is `__slots__` and when would you use it?"
- "Explain weak references"
- "How do you profile memory usage in Python?"

---

## Iterators, Generators, and Decorators

### Overview

Python's iteration protocol, generators, and decorators are powerful features that enable elegant, efficient code. These concepts are fundamental to understanding Python's design philosophy.

### Iterators

Objects that implement the iterator protocol (`__iter__` and `__next__`).

#### The Iterator Protocol

```python
# Any object with __iter__ and __next__ is an iterator

class CountDown:
    def __init__(self, start):
        self.current = start

    def __iter__(self):
        """Returns the iterator object (self)"""
        return self

    def __next__(self):
        """Returns the next value"""
        if self.current <= 0:
            raise StopIteration

        self.current -= 1
        return self.current + 1

# Using the iterator
counter = CountDown(5)
for num in counter:
    print(num)  # 5, 4, 3, 2, 1

# Manual iteration
counter = CountDown(3)
print(next(counter))  # 3
print(next(counter))  # 2
print(next(counter))  # 1
# print(next(counter))  # StopIteration exception

# Built-in iterables
my_list = [1, 2, 3]
iterator = iter(my_list)  # Get iterator from iterable
print(next(iterator))  # 1
print(next(iterator))  # 2
print(next(iterator))  # 3
```

#### Iterable vs Iterator

```python
# Iterable: has __iter__ method
# Iterator: has __iter__ and __next__ methods

# List is iterable but not iterator
my_list = [1, 2, 3]
print(hasattr(my_list, '__iter__'))   # True
print(hasattr(my_list, '__next__'))   # False

# Get iterator from iterable
iterator = iter(my_list)
print(hasattr(iterator, '__iter__'))  # True
print(hasattr(iterator, '__next__'))  # True

# For loop internally uses iterators
# This:
for item in my_list:
    print(item)

# Is equivalent to:
iterator = iter(my_list)
while True:
    try:
        item = next(iterator)
        print(item)
    except StopIteration:
        break
```

```mermaid
graph TD
    A[Iterable Object] -->|iter__| B[Iterator Object]
    B -->|__next__| C[Item 1]
    B -->|__next__| D[Item 2]
    B -->|__next__| E[Item 3]
    B -->|__next__| F[StopIteration]

    style A fill:#E6F3FF
    style B fill:#FFE6A0
    style F fill:#FFB6C6
```

#### Custom Iterable Classes

```python
# Separate iterable and iterator classes
class MyRange:
    """Iterable that returns an iterator"""
    def __init__(self, start, end):
        self.start = start
        self.end = end

    def __iter__(self):
        """Return a new iterator"""
        return MyRangeIterator(self.start, self.end)

class MyRangeIterator:
    """Iterator for MyRange"""
    def __init__(self, start, end):
        self.current = start
        self.end = end

    def __iter__(self):
        return self

    def __next__(self):
        if self.current >= self.end:
            raise StopIteration
        value = self.current
        self.current += 1
        return value

# Multiple independent iterations
my_range = MyRange(0, 5)

# First iteration
for num in my_range:
    print(num, end=' ')  # 0 1 2 3 4
print()

# Second iteration (works because __iter__ returns new iterator)
for num in my_range:
    print(num, end=' ')  # 0 1 2 3 4
```

### Generators

Functions that use `yield` to return values lazily.

#### Generator Functions

```python
# Generator function (uses yield)
def count_up_to(n):
    count = 1
    while count <= n:
        yield count  # Yields value and suspends
        count += 1

# Generator object
gen = count_up_to(5)
print(type(gen))  # <class 'generator'>

# Iterate over generator
for num in gen:
    print(num)  # 1, 2, 3, 4, 5

# Manual iteration
gen = count_up_to(3)
print(next(gen))  # 1
print(next(gen))  # 2
print(next(gen))  # 3
# print(next(gen))  # StopIteration

# Generator state is preserved between yields
def stateful_generator():
    print("Start")
    yield 1
    print("After first yield")
    yield 2
    print("After second yield")
    yield 3
    print("End")

gen = stateful_generator()
print(next(gen))  # "Start" then 1
print(next(gen))  # "After first yield" then 2
print(next(gen))  # "After second yield" then 3
```

#### Generator Expressions

Like list comprehensions but with parentheses.

```python
# List comprehension - creates entire list
squares_list = [x**2 for x in range(10)]
print(type(squares_list))  # <class 'list'>

# Generator expression - lazy evaluation
squares_gen = (x**2 for x in range(10))
print(type(squares_gen))  # <class 'generator'>

# Generator uses minimal memory
import sys
print(sys.getsizeof(squares_list))  # ~200 bytes
print(sys.getsizeof(squares_gen))   # ~128 bytes

# Generator for large datasets
# Bad: loads all in memory
large_list = [i**2 for i in range(1000000)]

# Good: generates on demand
large_gen = (i**2 for i in range(1000000))

# Use in functions that accept iterables
print(sum(x**2 for x in range(100)))  # No list created!

# Chain generators
numbers = (x for x in range(100))
evens = (x for x in numbers if x % 2 == 0)
squares = (x**2 for x in evens)
print(list(squares)[:10])  # First 10 values
```

#### Generator Methods

```python
# send() - send value to generator
def echo():
    value = None
    while True:
        value = yield value

gen = echo()
next(gen)  # Prime the generator
print(gen.send("Hello"))  # Hello
print(gen.send("World"))  # World

# throw() - raise exception in generator
def error_handler():
    try:
        while True:
            value = yield
            print(f"Received: {value}")
    except ValueError:
        print("ValueError caught!")

gen = error_handler()
next(gen)
gen.send("Test")  # Received: Test
gen.throw(ValueError)  # ValueError caught!

# close() - stop generator
def infinite_counter():
    count = 0
    while True:
        yield count
        count += 1

gen = infinite_counter()
print(next(gen))  # 0
print(next(gen))  # 1
gen.close()
# print(next(gen))  # StopIteration
```

#### Yield From (Python 3.3+)

Delegate to another generator.

```python
# Without yield from
def chain(*iterables):
    for iterable in iterables:
        for item in iterable:
            yield item

# With yield from
def chain(*iterables):
    for iterable in iterables:
        yield from iterable

# Usage
for item in chain([1, 2], [3, 4], [5, 6]):
    print(item)  # 1, 2, 3, 4, 5, 6

# Flatten nested lists
def flatten(nested):
    for item in nested:
        if isinstance(item, list):
            yield from flatten(item)  # Recursive
        else:
            yield item

nested = [1, [2, 3, [4, 5]], 6, [7]]
print(list(flatten(nested)))  # [1, 2, 3, 4, 5, 6, 7]
```

### Practical Generator Patterns

```python
# 1. Infinite sequences
def fibonacci():
    """Infinite Fibonacci sequence"""
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# Take first 10
import itertools
print(list(itertools.islice(fibonacci(), 10)))

# 2. Pipeline processing
def read_lines(filename):
    """Read file line by line"""
    with open(filename) as f:
        for line in f:
            yield line.strip()

def filter_comments(lines):
    """Filter out comment lines"""
    for line in lines:
        if not line.startswith('#'):
            yield line

def extract_numbers(lines):
    """Extract numbers from lines"""
    for line in lines:
        if line.isdigit():
            yield int(line)

# Pipeline
# lines = read_lines('data.txt')
# no_comments = filter_comments(lines)
# numbers = extract_numbers(no_comments)
# print(sum(numbers))

# 3. Sliding window
def sliding_window(iterable, n):
    """Generate sliding windows of size n"""
    from collections import deque
    window = deque(maxlen=n)

    for item in iterable:
        window.append(item)
        if len(window) == n:
            yield tuple(window)

for window in sliding_window([1, 2, 3, 4, 5], 3):
    print(window)
# (1, 2, 3)
# (2, 3, 4)
# (3, 4, 5)

# 4. State machines
def traffic_light():
    """Traffic light state machine"""
    while True:
        yield "Green"
        yield "Yellow"
        yield "Red"

lights = traffic_light()
for _ in range(6):
    print(next(lights))  # Green, Yellow, Red, Green, Yellow, Red

# 5. Batching
def batch(iterable, batch_size):
    """Yield batches from iterable"""
    batch = []
    for item in iterable:
        batch.append(item)
        if len(batch) == batch_size:
            yield batch
            batch = []
    if batch:  # Yield remaining items
        yield batch

for b in batch(range(10), 3):
    print(b)
# [0, 1, 2]
# [3, 4, 5]
# [6, 7, 8]
# [9]
```

### Decorators (Advanced)

#### Function Decorators

```python
# Basic decorator
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before function")
        result = func(*args, **kwargs)
        print("After function")
        return result
    return wrapper

@my_decorator
def greet(name):
    print(f"Hello, {name}!")
    return name

greet("Alice")
# Before function
# Hello, Alice!
# After function

# Decorator with arguments
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def say_hello():
    print("Hello!")

say_hello()
# Hello!
# Hello!
# Hello!

# Stacking decorators
@decorator1
@decorator2
@decorator3
def func():
    pass

# Equivalent to:
# func = decorator1(decorator2(decorator3(func)))
```

#### Preserving Metadata

```python
from functools import wraps

# Without @wraps
def bad_decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@bad_decorator
def greet(name):
    """Greet someone"""
    return f"Hello, {name}"

print(greet.__name__)  # "wrapper" (wrong!)
print(greet.__doc__)   # None (lost!)

# With @wraps
def good_decorator(func):
    @wraps(func)  # Preserves func metadata
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@good_decorator
def greet(name):
    """Greet someone"""
    return f"Hello, {name}"

print(greet.__name__)  # "greet" (correct!)
print(greet.__doc__)   # "Greet someone" (preserved!)
```

#### Class Decorators

```python
# Decorator class
class CountCalls:
    def __init__(self, func):
        self.func = func
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"Call {self.count} to {self.func.__name__}")
        return self.func(*args, **kwargs)

@CountCalls
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")  # Call 1 to greet
greet("Bob")    # Call 2 to greet

# Class decorator (decorates a class)
def add_str_method(cls):
    cls.__str__ = lambda self: f"{cls.__name__} instance"
    return cls

@add_str_method
class MyClass:
    pass

obj = MyClass()
print(obj)  # MyClass instance
```

#### Practical Decorators

```python
# 1. Timing decorator
import time
from functools import wraps

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        end = time.perf_counter()
        print(f"{func.__name__} took {end - start:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)
    return "Done"

slow_function()  # slow_function took 1.0001s

# 2. Memoization decorator
def memoize(func):
    cache = {}
    @wraps(func)
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrapper

@memoize
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(100))  # Fast with memoization!

# 3. Validation decorator
def validate_positive(func):
    @wraps(func)
    def wrapper(x):
        if x < 0:
            raise ValueError("Must be positive")
        return func(x)
    return wrapper

@validate_positive
def square_root(x):
    return x ** 0.5

print(square_root(16))  # 4.0
# square_root(-1)  # ValueError

# 4. Retry decorator
def retry(max_attempts=3, delay=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    print(f"Attempt {attempt + 1} failed: {e}")
                    time.sleep(delay)
        return wrapper
    return decorator

@retry(max_attempts=3, delay=0.1)
def unreliable_function():
    import random
    if random.random() < 0.7:
        raise Exception("Random failure")
    return "Success"

# 5. Singleton decorator
def singleton(cls):
    instances = {}
    @wraps(cls)
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance

@singleton
class Database:
    def __init__(self):
        print("Creating database connection")

db1 = Database()  # Creating database connection
db2 = Database()  # No output - returns same instance
print(db1 is db2)  # True
```

### Practical Example: Data Processing Pipeline

```python
from functools import wraps
import time

# Decorators for monitoring
def log_execution(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Executing {func.__name__}...")
        result = func(*args, **kwargs)
        print(f"Finished {func.__name__}")
        return result
    return wrapper

def measure_time(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed = time.perf_counter() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

# Generator-based data pipeline
class DataPipeline:
    """Memory-efficient data processing pipeline"""

    @staticmethod
    def read_data(filename):
        """Generator: read large file line by line"""
        print(f"Reading from {filename}")
        with open(filename, 'r') as f:
            for line in f:
                yield line.strip()

    @staticmethod
    def filter_valid(lines):
        """Generator: filter valid lines"""
        for line in lines:
            if line and not line.startswith('#'):
                yield line

    @staticmethod
    def parse_numbers(lines):
        """Generator: parse numbers from lines"""
        for line in lines:
            try:
                yield int(line)
            except ValueError:
                continue

    @staticmethod
    def transform(numbers, func):
        """Generator: apply transformation"""
        for num in numbers:
            yield func(num)

    @staticmethod
    @log_execution
    @measure_time
    def process(filename, transform_func):
        """Process data through pipeline"""
        # Chain generators
        lines = DataPipeline.read_data(filename)
        valid_lines = DataPipeline.filter_valid(lines)
        numbers = DataPipeline.parse_numbers(valid_lines)
        transformed = DataPipeline.transform(numbers, transform_func)

        # Consume generator and return results
        return list(transformed)

# Usage
# results = DataPipeline.process('numbers.txt', lambda x: x ** 2)

# Custom iterator for batched processing
class BatchIterator:
    """Iterator that yields batches"""

    def __init__(self, data, batch_size):
        self.data = data
        self.batch_size = batch_size
        self.index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.index >= len(self.data):
            raise StopIteration

        batch = self.data[self.index:self.index + self.batch_size]
        self.index += self.batch_size
        return batch

# Process in batches
data = list(range(100))
for batch in BatchIterator(data, 10):
    # Process batch
    print(f"Processing batch of {len(batch)} items")
```

### Interview Insight: Why This Matters

Understanding iterators, generators, and decorators helps you:

- Write memory-efficient code
- Create elegant, reusable abstractions
- Implement lazy evaluation patterns
- Build powerful function composition

Common interview questions from this section:

- "What's the difference between an iterator and an iterable?"
- "Explain how generators work internally"
- "What's the benefit of generators over lists?"
- "What does `yield from` do?"
- "Explain how decorators work"
- "What is `functools.wraps` and why use it?"
- "Can you write a memoization decorator?"
- "What are generator expressions?"
- "Explain the iterator protocol"

---

## Basic Concurrency (Threading, Multiprocessing, Async)

### Overview

Python provides several mechanisms for concurrent execution: threading for I/O-bound tasks, multiprocessing for CPU-bound tasks, and async/await for asynchronous I/O. Understanding when to use each is crucial for performance.

### Threading

Threads share memory space but only one thread executes Python bytecode at a time (GIL).

#### Basic Threading

```python
import threading
import time

# Create and start thread
def worker(name, duration):
    print(f"{name}: Starting")
    time.sleep(duration)
    print(f"{name}: Finished")

# Method 1: Create thread
t1 = threading.Thread(target=worker, args=("Thread-1", 2))
t1.start()  # Start thread
t1.join()   # Wait for completion

# Method 2: Multiple threads
threads = []
for i in range(5):
    t = threading.Thread(target=worker, args=(f"Thread-{i}", 1))
    t.start()
    threads.append(t)

# Wait for all threads
for t in threads:
    t.join()

# Thread with return value (using queue)
from queue import Queue

def worker_with_result(name, queue):
    result = f"{name}: Result"
    queue.put(result)

result_queue = Queue()
t = threading.Thread(target=worker_with_result, args=("Worker", result_queue))
t.start()
t.join()
print(result_queue.get())  # Worker: Result
```

#### Thread Synchronization

```python
# Problem: Race condition
counter = 0

def increment():
    global counter
    for _ in range(100000):
        counter += 1  # NOT atomic!

threads = [threading.Thread(target=increment) for _ in range(10)]
for t in threads:
    t.start()
for t in threads:
    t.join()

print(counter)  # Likely not 1000000! Race condition

# Solution 1: Lock
counter = 0
lock = threading.Lock()

def increment_safe():
    global counter
    for _ in range(100000):
        with lock:  # Acquire lock
            counter += 1
        # Lock released

threads = [threading.Thread(target=increment_safe) for _ in range(10)]
for t in threads:
    t.start()
for t in threads:
    t.join()

print(counter)  # 1000000 - correct!

# Solution 2: RLock (Reentrant Lock)
rlock = threading.RLock()

def recursive_function(n):
    with rlock:
        if n > 0:
            recursive_function(n - 1)  # Can acquire same lock again

# Solution 3: Semaphore (limit concurrent access)
semaphore = threading.Semaphore(3)  # Max 3 threads

def limited_worker(name):
    with semaphore:
        print(f"{name}: Working")
        time.sleep(1)

# Only 3 threads work at a time
threads = [threading.Thread(target=limited_worker, args=(f"T{i}",)) for i in range(10)]
for t in threads:
    t.start()
for t in threads:
    t.join()

# Solution 4: Event (signal between threads)
event = threading.Event()

def waiter():
    print("Waiting for event...")
    event.wait()  # Block until event is set
    print("Event received!")

def setter():
    time.sleep(2)
    print("Setting event")
    event.set()

t1 = threading.Thread(target=waiter)
t2 = threading.Thread(target=setter)
t1.start()
t2.start()
t1.join()
t2.join()
```

```mermaid
sequenceDiagram
    participant T1 as Thread 1
    participant L as Lock
    participant T2 as Thread 2

    T1->>L: Acquire lock
    L-->>T1: Lock granted
    T2->>L: Try to acquire lock
    L-->>T2: Block (wait)
    T1->>T1: Critical section
    T1->>L: Release lock
    L-->>T2: Lock granted
    T2->>T2: Critical section
    T2->>L: Release lock
```

#### Thread Pools

```python
from concurrent.futures import ThreadPoolExecutor
import time

def task(n):
    print(f"Task {n}: Starting")
    time.sleep(1)
    return f"Task {n}: Result"

# Create thread pool
with ThreadPoolExecutor(max_workers=5) as executor:
    # Submit single task
    future = executor.submit(task, 1)
    print(future.result())  # Wait and get result

    # Submit multiple tasks
    futures = [executor.submit(task, i) for i in range(10)]

    # Get results as they complete
    from concurrent.futures import as_completed
    for future in as_completed(futures):
        print(future.result())

# map() - process iterable
with ThreadPoolExecutor(max_workers=5) as executor:
    results = executor.map(task, range(10))
    for result in results:
        print(result)
```

### Multiprocessing

Separate processes with independent memory - bypasses GIL.

#### Basic Multiprocessing

```python
import multiprocessing
import os

def worker(name):
    print(f"{name}: PID {os.getpid()}")
    return f"{name}: Done"

# Create and start process
p = multiprocessing.Process(target=worker, args=("Process-1",))
p.start()
p.join()

# Multiple processes
processes = []
for i in range(5):
    p = multiprocessing.Process(target=worker, args=(f"Process-{i}",))
    p.start()
    processes.append(p)

for p in processes:
    p.join()

# Process with return value (using Queue)
def worker_with_result(name, queue):
    result = f"{name}: Result"
    queue.put(result)

queue = multiprocessing.Queue()
p = multiprocessing.Process(target=worker_with_result, args=("Worker", queue))
p.start()
p.join()
print(queue.get())
```

#### Process Pools

```python
from multiprocessing import Pool

def square(n):
    return n * n

# Create process pool
with Pool(processes=4) as pool:
    # map() - process iterable
    results = pool.map(square, range(10))
    print(results)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

    # apply() - single task
    result = pool.apply(square, (5,))
    print(result)  # 25

    # starmap() - multiple arguments
    def add(a, b):
        return a + b

    results = pool.starmap(add, [(1, 2), (3, 4), (5, 6)])
    print(results)  # [3, 7, 11]

# Process Pool Executor (unified interface)
from concurrent.futures import ProcessPoolExecutor

with ProcessPoolExecutor(max_workers=4) as executor:
    results = executor.map(square, range(10))
    print(list(results))
```

#### Shared Memory

```python
from multiprocessing import Process, Value, Array, Manager

# Shared value
def increment(counter, lock):
    for _ in range(100000):
        with lock:
            counter.value += 1

counter = Value('i', 0)  # Shared integer
lock = multiprocessing.Lock()

processes = [Process(target=increment, args=(counter, lock)) for _ in range(10)]
for p in processes:
    p.start()
for p in processes:
    p.join()

print(counter.value)  # 1000000

# Shared array
def fill_array(arr, index, value):
    arr[index] = value

arr = Array('i', 10)  # Shared array of 10 integers
processes = [Process(target=fill_array, args=(arr, i, i*2)) for i in range(10)]
for p in processes:
    p.start()
for p in processes:
    p.join()

print(list(arr))  # [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

# Manager (more flexible, slower)
def modify_dict(d, key, value):
    d[key] = value

manager = Manager()
shared_dict = manager.dict()

processes = [Process(target=modify_dict, args=(shared_dict, i, i*2)) for i in range(5)]
for p in processes:
    p.start()
for p in processes:
    p.join()

print(dict(shared_dict))  # {0: 0, 1: 2, 2: 4, 3: 6, 4: 8}
```

### Async/Await (Asyncio)

Asynchronous I/O using coroutines and event loop.

#### Basic Async

```python
import asyncio

# Async function (coroutine)
async def say_hello(name, delay):
    print(f"{name}: Starting")
    await asyncio.sleep(delay)  # Non-blocking sleep
    print(f"{name}: Finished")
    return f"{name}: Done"

# Run single coroutine
result = asyncio.run(say_hello("Task-1", 1))
print(result)

# Run multiple coroutines concurrently
async def main():
    # Create tasks
    task1 = asyncio.create_task(say_hello("Task-1", 2))
    task2 = asyncio.create_task(say_hello("Task-2", 1))
    task3 = asyncio.create_task(say_hello("Task-3", 3))

    # Wait for all tasks
    results = await asyncio.gather(task1, task2, task3)
    print(results)

asyncio.run(main())

# Using await
async def fetch_data(url):
    await asyncio.sleep(1)  # Simulate network request
    return f"Data from {url}"

async def main():
    data = await fetch_data("example.com")
    print(data)

asyncio.run(main())
```

#### Async Patterns

```python
# Pattern 1: gather() - run concurrently, wait for all
async def task(n):
    await asyncio.sleep(n)
    return n

async def main():
    results = await asyncio.gather(
        task(1),
        task(2),
        task(3)
    )
    print(results)  # [1, 2, 3]

asyncio.run(main())

# Pattern 2: wait() - more control
async def main():
    tasks = [asyncio.create_task(task(i)) for i in range(1, 4)]

    # Wait for first completion
    done, pending = await asyncio.wait(tasks, return_when=asyncio.FIRST_COMPLETED)

    # Cancel remaining
    for t in pending:
        t.cancel()

# Pattern 3: as_completed() - process as they finish
async def main():
    tasks = [task(i) for i in [3, 1, 2]]

    for coro in asyncio.as_completed(tasks):
        result = await coro
        print(f"Completed: {result}")

asyncio.run(main())

# Pattern 4: Timeout
async def slow_task():
    await asyncio.sleep(5)
    return "Done"

async def main():
    try:
        result = await asyncio.wait_for(slow_task(), timeout=2)
    except asyncio.TimeoutError:
        print("Task timed out")

asyncio.run(main())

# Pattern 5: Async context manager
class AsyncResource:
    async def __aenter__(self):
        print("Acquiring resource")
        await asyncio.sleep(1)
        return self

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("Releasing resource")
        await asyncio.sleep(1)

async def main():
    async with AsyncResource() as resource:
        print("Using resource")

asyncio.run(main())

# Pattern 6: Async iterator
class AsyncCounter:
    def __init__(self, stop):
        self.current = 0
        self.stop = stop

    def __aiter__(self):
        return self

    async def __anext__(self):
        if self.current >= self.stop:
            raise StopAsyncIteration

        await asyncio.sleep(0.1)
        self.current += 1
        return self.current

async def main():
    async for num in AsyncCounter(5):
        print(num)

asyncio.run(main())
```

### Choosing the Right Approach

```python
# CPU-bound task: Use multiprocessing
def cpu_intensive(n):
    """Compute-heavy operation"""
    return sum(i * i for i in range(n))

# Good: Multiprocessing
from multiprocessing import Pool
with Pool() as pool:
    results = pool.map(cpu_intensive, [10000000] * 4)

# Bad: Threading (GIL prevents parallel execution)
# from concurrent.futures import ThreadPoolExecutor
# with ThreadPoolExecutor() as executor:
#     results = executor.map(cpu_intensive, [10000000] * 4)

# I/O-bound task: Use threading or async
import requests

def download(url):
    """Network I/O operation"""
    response = requests.get(url)
    return len(response.content)

# Good: Threading
from concurrent.futures import ThreadPoolExecutor
urls = ["http://example.com"] * 10
with ThreadPoolExecutor() as executor:
    results = executor.map(download, urls)

# Also good: Async (even better for I/O)
import aiohttp

async def download_async(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return len(await response.read())

async def main():
    tasks = [download_async(url) for url in urls]
    results = await asyncio.gather(*tasks)

# asyncio.run(main())
```

```mermaid
graph TD
    A[Concurrency Problem] --> B{Task Type?}
    B -->|CPU-Bound| C[Multiprocessing]
    B -->|I/O-Bound| D{Style?}
    D -->|Callback-based| E[Asyncio]
    D -->|Blocking I/O| F[Threading]
    D -->|Simple| F

    C --> C1[Separate processes<br/>Bypass GIL<br/>Higher overhead]
    F --> F1[Shared memory<br/>GIL limits<br/>Good for I/O]
    E --> E1[Single thread<br/>Event loop<br/>Best for many I/O]

    style C1 fill:#90EE90
    style F1 fill:#FFE6A0
    style E1 fill:#E6F3FF
```

### Practical Example: Concurrent Web Scraper

```python
import asyncio
import aiohttp
import time
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

class WebScraper:
    """Compare different concurrency approaches"""

    @staticmethod
    def scrape_sync(urls):
        """Synchronous - baseline"""
        import requests

        start = time.time()
        results = []

        for url in urls:
            response = requests.get(url)
            results.append(len(response.content))

        elapsed = time.time() - start
        print(f"Sync: {elapsed:.2f}s")
        return results

    @staticmethod
    def scrape_threaded(urls):
        """Threading - good for I/O"""
        import requests

        def fetch(url):
            response = requests.get(url)
            return len(response.content)

        start = time.time()

        with ThreadPoolExecutor(max_workers=10) as executor:
            results = list(executor.map(fetch, urls))

        elapsed = time.time() - start
        print(f"Threaded: {elapsed:.2f}s")
        return results

    @staticmethod
    async def scrape_async(urls):
        """Async - best for I/O"""

        async def fetch(session, url):
            async with session.get(url) as response:
                return len(await response.read())

        start = time.time()

        async with aiohttp.ClientSession() as session:
            tasks = [fetch(session, url) for url in urls]
            results = await asyncio.gather(*tasks)

        elapsed = time.time() - start
        print(f"Async: {elapsed:.2f}s")
        return results

# Usage
urls = ["http://example.com"] * 20

# WebScraper.scrape_sync(urls)      # ~20s (sequential)
# WebScraper.scrape_threaded(urls)  # ~2s (concurrent)
# asyncio.run(WebScraper.scrape_async(urls))  # ~1s (fastest!)
```

### Interview Insight: Why This Matters

Understanding concurrency helps you:

- Optimize performance for I/O and CPU-bound tasks
- Choose appropriate concurrency model
- Avoid race conditions and deadlocks
- Build scalable applications

Common interview questions from this section:

- "Explain Python's GIL and its implications"
- "When would you use threading vs multiprocessing?"
- "What is asyncio and when should you use it?"
- "Explain race conditions and how to prevent them"
- "What's the difference between concurrent and parallel execution?"
- "How does async/await work in Python?"
- "What are the trade-offs between different concurrency models?"
- "Explain thread synchronization primitives (Lock, Semaphore, Event)"

---

## Interview Preparation

### Overview

This section compiles frequently asked Python interview questions from product-based companies, covering conceptual, coding, debugging, and scenario-based questions. Each question includes the "why" behind it and what interviewers are evaluating.

### Conceptual Questions

#### 1. Core Python Concepts

**Q: What is Python? What are its key features?**

_Why asked:_ Tests basic understanding and communication skills.

_Answer:_

- Interpreted, high-level, dynamically-typed language
- Key features:
    - Easy to learn and read (clean syntax)
    - Extensive standard library
    - Cross-platform compatibility
    - Dynamic typing
    - Automatic memory management
    - Strong community and ecosystem
    - Supports multiple paradigms (OOP, functional, procedural)

**Q: Explain Python's GIL. What are its implications?**

_Why asked:_ Tests understanding of Python's internals and concurrency.

_Answer:_

- GIL (Global Interpreter Lock) allows only one thread to execute Python bytecode at a time
- Implications:
    - Multi-threading doesn't provide true parallelism for CPU-bound tasks
    - I/O-bound tasks still benefit from threading
    - Workarounds: multiprocessing, asyncio, C extensions
- Exists because: Python's memory management is not thread-safe

```python
# GIL impact demonstration
import threading
import time

# CPU-bound task - NO speedup with threading
def cpu_task():
    sum(i*i for i in range(10000000))

start = time.time()
t1 = threading.Thread(target=cpu_task)
t2 = threading.Thread(target=cpu_task)
t1.start(); t2.start()
t1.join(); t2.join()
print(f"Threading: {time.time() - start:.2f}s")  # Slow due to GIL

# Use multiprocessing for CPU-bound tasks
from multiprocessing import Process
start = time.time()
p1 = Process(target=cpu_task)
p2 = Process(target=cpu_task)
p1.start(); p2.start()
p1.join(); p2.join()
print(f"Multiprocessing: {time.time() - start:.2f}s")  # Faster
```

**Q: What's the difference between `is` and `==`?**

_Why asked:_ Tests understanding of object identity vs value equality.

_Answer:_

- `==` compares values (calls `__eq__`)
- `is` compares identity (memory address)
- Use `is` for singleton objects: `None`, `True`, `False`

```python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)  # True (same values)
print(a is b)  # False (different objects)
print(a is c)  # True (same object)

# Integer caching
x = 256
y = 256
print(x is y)  # True (small ints are cached)

x = 257
y = 257
print(x is y)  # False (larger ints may not be cached)
```

**Q: Explain mutable vs immutable types**

_Why asked:_ Critical for understanding Python's behavior and avoiding bugs.

_Answer:_

- Immutable: `int`, `float`, `str`, `tuple`, `frozenset`, `bytes`
    - Cannot be modified after creation
    - Operations create new objects
    - Can be dictionary keys
    - Thread-safe by nature

- Mutable: `list`, `dict`, `set`, `bytearray`, user-defined classes
    - Can be modified in-place
    - Cannot be dictionary keys (unhashable)
    - Require careful handling when passing to functions

```python
# Immutable - new object created
s = "hello"
id_before = id(s)
s += " world"
print(id(s) == id_before)  # False

# Mutable - same object modified
lst = [1, 2, 3]
id_before = id(lst)
lst.append(4)
print(id(lst) == id_before)  # True

# Common pitfall: mutable default argument
def bad_function(lst=[]):  # DANGEROUS!
    lst.append(1)
    return lst

print(bad_function())  # [1]
print(bad_function())  # [1, 1] - Same list!

# Correct approach
def good_function(lst=None):
    if lst is None:
        lst = []
    lst.append(1)
    return lst
```

**Q: What are \*args and **kwargs?\*\*

_Why asked:_ Tests understanding of flexible function signatures.

_Answer:_

- `*args`: Variable positional arguments (tuple)
- `**kwargs`: Variable keyword arguments (dict)

```python
def func(*args, **kwargs):
    print(f"Args: {args}")      # Tuple
    print(f"Kwargs: {kwargs}")  # Dict

func(1, 2, 3, name="Alice", age=30)
# Args: (1, 2, 3)
# Kwargs: {'name': 'Alice', 'age': 30}

# Unpacking
numbers = [1, 2, 3]
func(*numbers)  # Unpacks list as positional args

data = {'x': 10, 'y': 20}
func(**data)  # Unpacks dict as keyword args
```

#### 2. Object-Oriented Programming

**Q: Explain the four pillars of OOP**

_Why asked:_ Fundamental OOP knowledge.

_Answer:_

1. **Encapsulation**: Bundle data and methods, hide internal details
2. **Inheritance**: Derive classes from base classes
3. **Polymorphism**: Same interface, different implementations
4. **Abstraction**: Hide complex implementation, show only essential features

```python
# Encapsulation
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # Private

    def deposit(self, amount):
        self.__balance += amount

# Inheritance
class SavingsAccount(BankAccount):
    def __init__(self, balance, interest_rate):
        super().__init__(balance)
        self.interest_rate = interest_rate

# Polymorphism
class Dog:
    def speak(self):
        return "Woof!"

class Cat:
    def speak(self):
        return "Meow!"

def animal_sound(animal):
    print(animal.speak())  # Same interface

# Abstraction
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass
```

**Q: What's the difference between `@classmethod` and `@staticmethod`?**

_Why asked:_ Tests understanding of different method types.

_Answer:_

```python
class MyClass:
    class_var = 0

    def instance_method(self):
        # Has access to instance (self)
        return f"Instance: {self}"

    @classmethod
    def class_method(cls):
        # Has access to class (cls)
        # Can access/modify class variables
        cls.class_var += 1
        return f"Class: {cls}"

    @staticmethod
    def static_method(x, y):
        # No access to instance or class
        # Just a regular function in class namespace
        return x + y

# Usage
obj = MyClass()
obj.instance_method()       # Needs instance
MyClass.class_method()      # Can call on class or instance
MyClass.static_method(1, 2) # Can call on class or instance

# Common use: alternative constructors
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def from_string(cls, date_string):
        y, m, d = map(int, date_string.split('-'))
        return cls(y, m, d)

date = Date.from_string("2026-05-15")
```

**Q: Explain method overriding vs method overloading**

_Why asked:_ Common confusion between OOP concepts.

_Answer:_

- **Method Overriding**: Subclass provides specific implementation of parent method
- **Method Overloading**: Python doesn't support traditional overloading (multiple methods with same name, different signatures)

```python
# Method Overriding
class Animal:
    def speak(self):
        return "Some sound"

class Dog(Animal):
    def speak(self):  # Override
        return "Woof!"

# Python's "overloading" alternatives
def add(a, b=0, c=0):  # Default arguments
    return a + b + c

def add(*args):  # Variable arguments
    return sum(args)

# Type-based dispatch
from functools import singledispatch

@singledispatch
def process(arg):
    print(f"Generic: {arg}")

@process.register(int)
def _(arg):
    print(f"Integer: {arg * 2}")

@process.register(str)
def _(arg):
    print(f"String: {arg.upper()}")
```

#### 3. Memory Management

**Q: How does Python manage memory?**

_Why asked:_ Tests understanding of internals.

_Answer:_

1. **Reference Counting**: Main mechanism; object deallocated when refcount reaches 0
2. **Garbage Collection**: Handles circular references using generational GC
3. **Memory Pools**: Python maintains pools of objects for efficiency

```python
import sys

x = [1, 2, 3]
print(sys.getrefcount(x))  # Reference count

# Circular reference example
class Node:
    def __init__(self):
        self.ref = None

a = Node()
b = Node()
a.ref = b
b.ref = a  # Circular reference

# Deleted by GC, not reference counting
del a, b
import gc
gc.collect()  # Force garbage collection
```

**Q: What is `__slots__` and when would you use it?**

_Why asked:_ Tests knowledge of memory optimization.

_Answer:_

- `__slots__` restricts instance attributes, saves memory
- Use when: Creating many instances of a class

```python
# Without __slots__
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

# With __slots__
class Point:
    __slots__ = ['x', 'y']

    def __init__(self, x, y):
        self.x = x
        self.y = y

# Memory savings: ~40% less memory
# Trade-off: Can't add new attributes dynamically
```

### Coding Challenges

#### 1. List and String Manipulation

**Q: Reverse a string without using built-in reverse**

```python
def reverse_string(s):
    # Method 1: Slicing
    return s[::-1]

    # Method 2: Two pointers
    chars = list(s)
    left, right = 0, len(chars) - 1
    while left < right:
        chars[left], chars[right] = chars[right], chars[left]
        left += 1
        right -= 1
    return ''.join(chars)

    # Method 3: Stack
    return ''.join(reversed(s))

print(reverse_string("hello"))  # "olleh"
```

**Q: Find all permutations of a string**

```python
def permutations(s):
    # Recursive approach
    if len(s) <= 1:
        return [s]

    result = []
    for i, char in enumerate(s):
        remaining = s[:i] + s[i+1:]
        for perm in permutations(remaining):
            result.append(char + perm)
    return result

# Using itertools
from itertools import permutations as perm
result = [''.join(p) for p in perm("abc")]
print(result)  # ['abc', 'acb', 'bac', 'bca', 'cab', 'cba']
```

**Q: Remove duplicates from list while preserving order**

```python
def remove_duplicates(lst):
    # Method 1: dict.fromkeys (Python 3.7+)
    return list(dict.fromkeys(lst))

    # Method 2: Set with order tracking
    seen = set()
    result = []
    for item in lst:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

lst = [1, 2, 2, 3, 1, 4, 3, 5]
print(remove_duplicates(lst))  # [1, 2, 3, 4, 5]
```

**Q: Find the first non-repeating character**

```python
def first_non_repeating(s):
    from collections import Counter

    counts = Counter(s)
    for char in s:
        if counts[char] == 1:
            return char
    return None

print(first_non_repeating("leetcode"))  # 'l'
print(first_non_repeating("aabb"))      # None
```

#### 2. Data Structures

**Q: Implement a stack using list**

```python
class Stack:
    def __init__(self):
        self.items = []

    def push(self, item):
        self.items.append(item)

    def pop(self):
        if not self.is_empty():
            return self.items.pop()
        raise IndexError("Pop from empty stack")

    def peek(self):
        if not self.is_empty():
            return self.items[-1]
        return None

    def is_empty(self):
        return len(self.items) == 0

    def size(self):
        return len(self.items)
```

**Q: Implement LRU Cache**

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity

    def get(self, key):
        if key not in self.cache:
            return -1
        # Move to end (most recently used)
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            # Update and move to end
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            # Remove least recently used (first item)
            self.cache.popitem(last=False)

# Or use functools.lru_cache decorator
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_function(n):
    # Computed result is cached
    return sum(i * i for i in range(n))
```

**Q: Flatten a nested list**

```python
def flatten(nested_list):
    # Recursive
    result = []
    for item in nested_list:
        if isinstance(item, list):
            result.extend(flatten(item))
        else:
            result.append(item)
    return result

# Iterative using stack
def flatten_iterative(nested_list):
    stack = list(reversed(nested_list))
    result = []

    while stack:
        item = stack.pop()
        if isinstance(item, list):
            stack.extend(reversed(item))
        else:
            result.append(item)

    return result

nested = [1, [2, 3, [4, 5]], 6, [7]]
print(flatten(nested))  # [1, 2, 3, 4, 5, 6, 7]
```

#### 3. Algorithms

**Q: Implement binary search**

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1

    while left <= right:
        mid = (left + right) // 2

        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1

# Recursive version
def binary_search_recursive(arr, target, left=0, right=None):
    if right is None:
        right = len(arr) - 1

    if left > right:
        return -1

    mid = (left + right) // 2

    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search_recursive(arr, target, mid + 1, right)
    else:
        return binary_search_recursive(arr, target, left, mid - 1)
```

**Q: Find two numbers that sum to target (Two Sum)**

```python
def two_sum(nums, target):
    # Hash map approach - O(n)
    seen = {}

    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i

    return None

print(two_sum([2, 7, 11, 15], 9))  # [0, 1]
```

**Q: Check if string is palindrome**

```python
def is_palindrome(s):
    # Method 1: Simple
    return s == s[::-1]

    # Method 2: Two pointers
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True

    # Method 3: Case-insensitive, alphanumeric only
    def is_palindrome_advanced(s):
        s = ''.join(c.lower() for c in s if c.isalnum())
        return s == s[::-1]

print(is_palindrome("racecar"))  # True
print(is_palindrome_advanced("A man, a plan, a canal: Panama"))  # True
```

**Q: Fibonacci sequence (multiple approaches)**

```python
# 1. Recursive (slow - O(2^n))
def fib_recursive(n):
    if n <= 1:
        return n
    return fib_recursive(n-1) + fib_recursive(n-2)

# 2. Memoization (top-down DP)
from functools import lru_cache

@lru_cache(maxsize=None)
def fib_memo(n):
    if n <= 1:
        return n
    return fib_memo(n-1) + fib_memo(n-2)

# 3. Tabulation (bottom-up DP)
def fib_dp(n):
    if n <= 1:
        return n

    dp = [0] * (n + 1)
    dp[1] = 1

    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]

    return dp[n]

# 4. Space-optimized
def fib_optimized(n):
    if n <= 1:
        return n

    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b

    return b

# 5. Generator
def fib_generator():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

gen = fib_generator()
print([next(gen) for _ in range(10)])
```

### Scenario-Based Questions

**Q: You have a large CSV file (10GB). How would you process it in Python?**

_What they're testing:_ Memory management, practical experience

_Answer:_

```python
# Bad: Load entire file
# with open('large.csv') as f:
#     data = f.readlines()  # Out of memory!

# Good: Process line by line
def process_large_csv(filename):
    import csv

    with open(filename, 'r') as f:
        reader = csv.reader(f)
        headers = next(reader)  # Read header

        for row in reader:  # Process one row at a time
            process_row(row)
            # Previous row can be garbage collected

# Better: Use pandas with chunking
import pandas as pd

chunk_size = 10000
for chunk in pd.read_csv('large.csv', chunksize=chunk_size):
    process_chunk(chunk)
    # Each chunk is processed and released

# Best: Use generators
def read_large_file(filename):
    with open(filename, 'r') as f:
        for line in f:
            yield process_line(line)

# Process without loading all into memory
for result in read_large_file('large.csv'):
    handle_result(result)
```

**Q: How would you debug a memory leak in Python?**

_What they're testing:_ Debugging skills, tooling knowledge

_Answer:_

```python
# 1. Use tracemalloc
import tracemalloc

tracemalloc.start()

# Code that might leak memory
data = []
for i in range(1000000):
    data.append([i] * 100)

snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')

print("Top 10 memory allocations:")
for stat in top_stats[:10]:
    print(stat)

tracemalloc.stop()

# 2. Use memory_profiler
# @profile decorator
# python -m memory_profiler script.py

# 3. Check for circular references
import gc
gc.set_debug(gc.DEBUG_LEAK)

# 4. Use objgraph
# import objgraph
# objgraph.show_most_common_types(limit=10)
# objgraph.show_growth()

# 5. Common causes:
# - Global variables holding references
# - Circular references
# - Not closing files/connections
# - Large caches without limits
# - Event listeners not removed
```

**Q: Design a rate limiter in Python**

_What they're testing:_ System design, practical implementation

_Answer:_

```python
import time
from collections import deque
from threading import Lock

class RateLimiter:
    """Token bucket rate limiter"""

    def __init__(self, max_requests, time_window):
        self.max_requests = max_requests
        self.time_window = time_window
        self.requests = deque()
        self.lock = Lock()

    def allow_request(self):
        with self.lock:
            now = time.time()

            # Remove old requests outside time window
            while self.requests and self.requests[0] < now - self.time_window:
                self.requests.popleft()

            # Check if we can accept new request
            if len(self.requests) < self.max_requests:
                self.requests.append(now)
                return True

            return False

# Usage: 5 requests per 60 seconds
limiter = RateLimiter(max_requests=5, time_window=60)

def api_call():
    if limiter.allow_request():
        print("Request allowed")
        # Process request
    else:
        print("Rate limit exceeded")

# Decorator version
from functools import wraps

def rate_limit(max_calls, period):
    calls = deque()
    lock = Lock()

    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            with lock:
                now = time.time()

                # Remove old calls
                while calls and calls[0] < now - period:
                    calls.popleft()

                if len(calls) >= max_calls:
                    raise Exception("Rate limit exceeded")

                calls.append(now)
                return func(*args, **kwargs)

        return wrapper
    return decorator

@rate_limit(max_calls=5, period=60)
def api_endpoint():
    return "Success"
```

### Company-Specific Topics

#### Google/Meta/Amazon

Common focus areas:

- **Algorithms & Data Structures**: Strong emphasis
- **System Design**: Scalability, distributed systems
- **Code Quality**: Clean, maintainable code
- **Problem-Solving**: Think out loud, optimize

**Sample Question: Implement a web crawler**

```python
from collections import deque
from urllib.parse import urljoin, urlparse
import requests
from bs4 import BeautifulSoup

class WebCrawler:
    def __init__(self, max_pages=100):
        self.max_pages = max_pages
        self.visited = set()
        self.queue = deque()

    def is_valid_url(self, url):
        parsed = urlparse(url)
        return bool(parsed.netloc) and bool(parsed.scheme)

    def get_links(self, url):
        try:
            response = requests.get(url, timeout=5)
            soup = BeautifulSoup(response.content, 'html.parser')

            links = []
            for link in soup.find_all('a'):
                href = link.get('href')
                if href:
                    full_url = urljoin(url, href)
                    if self.is_valid_url(full_url):
                        links.append(full_url)

            return links
        except Exception as e:
            print(f"Error crawling {url}: {e}")
            return []

    def crawl(self, start_url):
        self.queue.append(start_url)

        while self.queue and len(self.visited) < self.max_pages:
            url = self.queue.popleft()

            if url in self.visited:
                continue

            print(f"Crawling: {url}")
            self.visited.add(url)

            # Get links from page
            links = self.get_links(url)

            # Add new links to queue
            for link in links:
                if link not in self.visited:
                    self.queue.append(link)

        return self.visited

# crawler = WebCrawler(max_pages=50)
# pages = crawler.crawl("https://example.com")
```

#### Microsoft

Focus areas:

- **OOP Design**: Strong emphasis on design patterns
- **Problem Solving**: Multiple approaches
- **Code Review**: Reading and improving code

**Sample: Implement a caching decorator with TTL**

```python
import time
from functools import wraps

def cache_with_ttl(ttl_seconds=60):
    def decorator(func):
        cache = {}

        @wraps(func)
        def wrapper(*args):
            now = time.time()

            # Check if cached and not expired
            if args in cache:
                result, timestamp = cache[args]
                if now - timestamp < ttl_seconds:
                    print(f"Cache hit for {args}")
                    return result

            # Compute and cache
            print(f"Cache miss for {args}")
            result = func(*args)
            cache[args] = (result, now)

            # Clean expired entries
            expired = [k for k, (_, ts) in cache.items()
                      if now - ts >= ttl_seconds]
            for key in expired:
                del cache[key]

            return result

        return wrapper
    return decorator

@cache_with_ttl(ttl_seconds=5)
def expensive_operation(n):
    time.sleep(2)
    return n * n

print(expensive_operation(5))  # Cache miss - slow
print(expensive_operation(5))  # Cache hit - fast
time.sleep(6)
print(expensive_operation(5))  # Cache expired - slow
```

### Python-Specific Tricky Questions

**Q: What is the output?**

```python
# Question 1
x = [1, 2, 3]
y = x
y.append(4)
print(x)  # [1, 2, 3, 4] - same object!

# Question 2
def foo(a, b=[]):
    b.append(a)
    return b

print(foo(1))  # [1]
print(foo(2))  # [1, 2] - Same list!

# Question 3
x = 5
def func():
    x = 10

func()
print(x)  # 5 - function has local x

# Question 4
class Test:
    count = 0

    def __init__(self):
        Test.count += 1

t1 = Test()
t2 = Test()
print(Test.count)  # 2

# Question 5
print([] == [])  # True (same value)
print([] is [])  # False (different objects)
```

**Q: Explain this behavior:**

```python
# Late binding closure
funcs = []
for i in range(3):
    funcs.append(lambda: i)

print([f() for f in funcs])  # [2, 2, 2] not [0, 1, 2]!

# Why? 'i' is looked up when function is called, not when defined
# Fix:
funcs = []
for i in range(3):
    funcs.append(lambda i=i: i)  # Capture current value

print([f() for f in funcs])  # [0, 1, 2]
```

### Best Practices to Mention

1. **Code Style**: Follow PEP 8
2. **Documentation**: Use docstrings
3. **Error Handling**: Specific exceptions, proper cleanup
4. **Testing**: Write unit tests
5. **Type Hints**: Use for clarity (Python 3.5+)
6. **Context Managers**: Use `with` for resources
7. **Comprehensions**: Use when appropriate (readability matters)
8. **Generators**: For large datasets
9. **Virtual Environments**: Always use for projects
10. **Version Control**: Git best practices

### Interview Tips

1. **Communicate**: Think out loud, explain your approach
2. **Ask Questions**: Clarify requirements before coding
3. **Start Simple**: Basic solution first, then optimize
4. **Test**: Walk through test cases, edge cases
5. **Time/Space Complexity**: Always discuss Big O
6. **Multiple Solutions**: Show you know different approaches
7. **Clean Code**: Readable variable names, proper structure
8. **Know Trade-offs**: Discuss pros/cons of your approach

### Quick Reference: Time Complexities

```python
# List operations
lst.append(x)      # O(1)
lst.pop()          # O(1)
lst.pop(0)         # O(n)
lst.insert(0, x)   # O(n)
lst[i]             # O(1)
x in lst           # O(n)
lst.sort()         # O(n log n)

# Dict operations
d[key] = value     # O(1) average
d[key]             # O(1) average
key in d           # O(1) average
del d[key]         # O(1) average

# Set operations
s.add(x)           # O(1) average
x in s             # O(1) average
s1 | s2            # O(len(s1) + len(s2))
s1 & s2            # O(min(len(s1), len(s2)))

# String operations
s1 + s2            # O(n + m)
s.find(sub)        # O(n)
s.replace(old, new)# O(n)
```

### Final Words

This guide covers Python fundamentals comprehensively. For interviews:

1. **Practice Coding**: LeetCode, HackerRank, CodeSignal
2. **Understand Concepts**: Don't just memorize
3. **Build Projects**: Apply knowledge practically
4. **Read Code**: Study Python's standard library
5. **Stay Updated**: Follow PEPs, new features
6. **Review Basics**: Even senior roles test fundamentals

**Key Topics by Company Type:**

- **Startups**: Practical skills, full-stack ability, quick learning
- **Big Tech**: Algorithms, system design, scalability
- **Financial**: Precision, performance, reliability
- **Data Science**: NumPy, Pandas, data manipulation

**Remember:** Interviewers look for:

- Problem-solving ability
- Code quality
- Communication skills
- Debugging approach
- Continuous learning mindset

---

## Conclusion

You now have a comprehensive Python fundamentals guide covering:

✓ Python execution model and syntax
✓ All data types and structures
✓ Control flow and pattern matching
✓ Functions and functional programming
✓ Object-oriented programming
✓ Exception handling
✓ Modules and package management
✓ Standard library essentials
✓ Memory management
✓ Iterators, generators, and decorators
✓ Concurrency (threading, multiprocessing, async)
✓ Interview preparation with common questions

**Next Steps:**

1. Practice coding challenges daily
2. Build projects using these concepts
3. Review sections regularly
4. Deep dive into areas of interest
5. Contribute to open-source projects

**Good luck with your Python journey and interviews!** 🐍

---

**Guide Complete: Python Fundamentals - Interview Ready Edition**

_Version 1.0 - May 2026_
