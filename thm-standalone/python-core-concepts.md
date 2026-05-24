---
icon: python
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/5f04259cf9bf5b57aed2c476-1779122607085
coverY: 0
coverHeight: 138
---

# Python: Core Concepts

**Date:** 24.03.2026

**Room Category:** Walkthrough

**Core Concept:** Building a foundational knowledge of Python core concepts, including data types, operators, strings, dictionaries, and loop structures.

Writing that script requires more than just basic variables and if statements. An analyst needs to know how to work with strings and store data in lists or dictionaries. Mastering a richer set of operators and a second type of loop known as the for loop is also required. This room covers exactly those components.

***

## Task 1: Introduction

This room and its companion, Python: Building Scripts, form a two-part series. In this first room, the focus is on how Python handles data and control flow. In the second room, that foundation is used to write functions, handle errors, work with files, and import libraries.

**Learning Objectives**

* Review Python variables, conditional statements, and loops.
* Work with Python data types and perform type conversions.
* Use formatted strings (f-strings) and augmented assignment operators.
* Manipulate strings using built-in methods.
* Store and retrieve data with lists and dictionaries.
* Use comparison, logical, arithmetic, and membership operators.
* Iterate over data using for loops, while loops, and range().
* Control loop flow with break and continue.

**Prerequisites**

It is recommended to complete the Python: Simple Demo room prior to starting this module.

**Machine Access**

Users can follow along by clicking the Start Machine button to launch the Target VM. Visual Studio Code (VS Code) will open automatically with a modern Python installation. All example scripts discussed in this room are saved in the `/home/ubuntu/Core-Concepts/` directory.

Question: Let's master Python's core concepts!

> **Answer:** No answer needed

***

## Task 2: Quick Review: Hello World, Variables, and Conditionals

The following subsections review foundational concepts. For those who completed the Python: Simple Demo room, this will be familiar ground.

**Hello World**

The simplest Python program outputs text to the screen using the `print()` function. Previously, `print()` was used to indicate that the computer had picked a secret number.

```python
# This is a comment. Python ignores lines starting with #.
print("Hello World")
```

A few important rules apply here. Lines starting with `#` are comments and are ignored by the computer. The `print()` function outputs whatever is placed between its parentheses. Text, which is called a string, must be enclosed in quotation marks. Either double or single quotes are acceptable.

**Variables and Data Types**

Variables store and update data. For example, a secret number can be stored in a variable called `secret`, while user input is stored in `guess`.

```python
food = "ice cream"   # a string (text)
money = 2000         # an integer (whole number)
```

Variables are powerful because their values can be changed throughout the execution of a program.

```python
age = 30
age = age + 1
print(age)   # 31
```

Every value in Python has a data type. The core types include `str` for text, `int` for whole numbers, `float` for decimals, `bool` for True/False logic, and `list` for ordered collections. A value's type can be checked using the `type()` function.

```python
username = "admin"
port = 8080
print(type(username))   # <class 'str'>
print(type(port))       # <class 'int'>
```

**Conditional Statements and Logical Operators**

Keywords like `if`, `elif`, and `else` are used to compare values.

```python
age = 18
if age < 17:
    print("You are NOT old enough to drive")
else:
    print("You are old enough to drive")
```

An `if` statement requires a condition and a colon. This is followed by an indented code block that runs only when the condition is True. An optional `else` block runs when the condition is False. Additional checks can be added using `elif`.

Comparison operators allow conditions to be built. These include `==` for equality, `!=` for inequality, `<` for less than, and `>` for greater than. Logical operators such as `and`, `or`, and `not` can combine multiple conditions together.

```python
name = "bob"
hungry = True

if name == "bob" and hungry == True:
    print("Bob is hungry")
elif name == "bob" and not hungry:
    print("Bob is not hungry")
else:
    print("Not sure who this is or if they are hungry")
```

A critical distinction is that `=` is the assignment operator used to store a value. The `==` operator is the equality operator used to compare two values.

**What's New: Type Conversion and f-strings**

The `input()` function always returns a string. When a user types a number, Python stores it as text. Python provides several built-in conversion functions such as `int()`, `float()`, `str()`, and `bool()`.

```python
text = input("Enter a port number: ")    # user types 443
print(type(text))                        # <class 'str'>

port = int(text)                         # convert to integer
print(type(port))                        # <class 'int'>
print(port + 1)                          # 444
```

Without the `int()` conversion, attempting to add a number to the text variable would cause an error.

**Formatted Strings (f-strings)**

Modern Python offers a clean syntax for printing variables called f-strings. An f-string starts with the letter `f` before the opening quotation mark. It allows variables to be embedded directly inside curly braces.

```python
username = "admin"
port = 443

# Old approach (comma-separated)
print("User", username, "is on port", port)

# Modern approach (f-string)
print(f"User {username} is on port {port}")
```

Both lines produce the same output, but the f-string version provides precise control over spacing and formatting.

**Augmented Assignment Operators**

Python provides a shorthand for incrementing counters. This pattern is known as augmented assignment.

```python
count = 0
count += 1    # same as count = count + 1
count -= 1    # same as count = count - 1
count *= 2    # same as count = count * 2
count /= 4    # same as count = count / 4
```

These shortcuts reduce the chance of typos and are frequently used in loops.

Question: What built-in function reveals the data type of a value?

> **Answer:** type()

Question: If a user types 3.14 at an input() prompt, what data type does Python store it as before any conversion?

> **Answer:** str

Question: On the attached VM, open and run fstrings\_demo.py. What is the last line printed to the terminal?

> **Answer:** Scan complete: 192.168.1.1 has 3 open ports

***

## Task 3: Working with Strings

Online forms often require passwords to contain specific characters, like uppercase letters. Behind those requirements is code that inspects the password character by character. In Python, that inspection is powered by string methods.

A string is a sequence of characters enclosed in quotes. Single quotes, double quotes, or triple quotes for multi-line text are all treated as the same data type.

**String Length**

The `len()` function returns how many characters a string contains. This is a fundamental check in security scripts to determine if an input is within expected bounds.

```python
password = "Tr0ub4dor"
length = len(password)
print(f"Password length: {length}")   # Password length: 9
```

**String Indexing and Slicing**

Each character in a string occupies a numbered position called an index. Python indexes start at 0.

```python
word = "Python"
print(word[0])     # P   (first character)
print(word[5])     # n   (last character)
print(word[-1])    # n   (negative index counts from the end)
```

Slicing extracts a portion of a string using the syntax `string[start:end]`. The start index is included, but the end index is excluded.

```python
word = "Python"
print(word[0:3])   # Pyt  (characters at index 0, 1, 2)
print(word[2:])    # thon (from index 2 to the end)
print(word[:4])    # Pyth (from the start to index 3)
```

**Useful String Methods**

Python strings come with built-in methods. Common methods include `.upper()` for uppercase, `.lower()` for lowercase, and `.strip()` to remove leading or trailing whitespace.

Other helpful methods are `.replace("a", "b")` to swap characters, and `.split(",")` to break a string into a list based on a separator.

**Character Checks**

For validation tasks, it is necessary to know whether a character is a digit or a letter. Python provides specific methods for this.

```python
char = "A"
print(char.isupper())    # True
print(char.islower())    # False
print(char.isdigit())    # False
print(char.isalpha())    # True   (is it a letter?)
print(char.isalnum())    # True   (is it a letter or digit?)
```

These methods return a boolean True or False, making them perfect for conditional statements.

**The in Operator**

The `in` operator checks whether a substring exists within a larger string.

```python
url = "https://tryhackme.com/room/pythoncoreconcepts"

print("tryhackme" in url)     # True
print("hackthebox" in url)    # False
print("https" in url)         # True
```

This operator is highly versatile and is also utilized with lists and dictionaries.

Question: What function returns the number of characters in a string?

> **Answer:** len()

Question: Given word = "TryHackMe", what does word\[3:7] return?

> **Answer:** Hack

Question: What string method converts "ADMIN" to "admin"?

> **Answer:** .lower()

***

## Task 4: Lists and Dictionaries

When monitoring a network, an analyst might have a list of IP addresses to scan and a collection of open ports for each host. Storing each of these in separate variables is impractical. Python uses lists and dictionaries to handle collections of data.

**Lists**

A list is an ordered collection of items enclosed in square brackets. Items can be of any data type, and the list preserves their order.

```python
ports = [22, 80, 443, 8080]
usernames = ["admin", "root", "guest"]
mixed = ["server1", 443, True]
```

**Accessing and Modifying List Elements**

Lists use the same indexing system as strings. The first element is always at index 0.

```python
ports = [22, 80, 443, 8080]

print(ports[0])      # 22
print(ports[-1])     # 8080   (last element)
print(ports[1:3])    # [80, 443]   (slicing works on lists too)
```

An element can be changed by assigning a new value to its index.

```python
ports[0] = 2222
print(ports)         # [2222, 80, 443, 8080]
```

**Common List Methods**

Common methods include `.append(x)` to add items to the end of the list and `.remove(x)` to delete the first occurrence of an item. The `.pop(i)` method removes and returns an item at a specific index. Lists can also be sorted using `.sort()` or reversed with `.reverse()`.

The `in` operator works perfectly on lists to check for existing elements.

```python
common_passwords = ["123456", "password", "admin", "letmein"]

if "password" in common_passwords:
    print("This password is in the common list.")
```

**Dictionaries**

A dictionary stores data as key-value pairs enclosed in curly braces. This structure allows one piece of data to be associated with another for quick retrieval.

```python
services = {
    22: "SSH",
    80: "HTTP",
    443: "HTTPS",
    3306: "MySQL"
}
```

Values are retrieved by passing the specific key inside square brackets.

```python
print(services[22])     # SSH
print(services[443])    # HTTPS
```

New entries can be added or existing ones updated dynamically.

```python
# Add a new entry
services[8080] = "HTTP-Alt"

# Update an existing entry
services[22] = "OpenSSH"

# Remove an entry
del services[3306]

print(services)
# {22: 'OpenSSH', 80: 'HTTP', 443: 'HTTPS', 8080: 'HTTP-Alt'}
```

The `.get()` method is extremely useful for dictionaries. Accessing a nonexistent key with square brackets causes an error, but `.get()` allows for a safe fallback value.

```python
result = services.get(9999, "Unknown")
print(result)   # Unknown
```

Question: What method adds an element to the end of a list?

> **Answer:** .append()

Question: Given services = {22: "SSH", 80: "HTTP"}, what does services\[80] return?

> **Answer:** HTTP

Question: What dictionary method lets you retrieve a value with a safe fallback if the key does not exist?

> **Answer:** .get()

***

## Task 5: Arithmetic and Membership Operators

Previous scripts used comparison operators like `<` and `>` to compare values. This task expands the operator toolkit with arithmetic operators and compound conditions.

**New Arithmetic Operators**

In addition to basic math symbols, Python includes specialized operators. The modulus operator `%` returns the remainder after division. It is commonly used to check whether a number is even or odd.

Floor division `//` divides and then rounds down to the nearest whole number. Regular division `/` always returns a float.

The exponent operator raises the left number to the power of the right number. For example, `2 8` calculates 2 raised to the 8th power, resulting in 256.

**The Membership Operator: in**

The `in` operator works with almost every collection type in Python and plays a central role in security scripts.

```python
# Checking if a password is in a list of common passwords
common = ["123456", "password", "qwerty", "letmein"]
user_password = "qwerty"

if user_password in common:
    print("This password is too common.")
```

It can be negated using `not in` to verify that an item is absent from a collection.

**Combining Operators in Practice**

Operators are frequently combined to create complex logical checks.

```python
password = "Tr0ubador"
length = len(password)
has_digit = any(char.isdigit() for char in password)

if length >= 8 and has_digit:
    print("Moderate strength")
elif length >= 8 or has_digit:
    print("Weak, but has some merit")
else:
    print("Very weak")
```

The `and` keyword requires both conditions to be true, while `or` requires only one condition to be true.

Question: What operator returns the remainder of a division?

> **Answer:** %

Question: What does 10 // 3 evaluate to?

> **Answer:** 3

Question: What does 2 10 evaluate to?

> **Answer:** 1024

***

## Task 6: Loops: for and while

A while loop repeats as long as a condition is true. It is perfect when the number of iterations depends on runtime conditions. Python also offers a second type of loop, the for loop, which is designed for iterating over a sequence of items.

**Quick Recap: while Loops**

A while loop runs as long as its condition evaluates to True. Once the condition becomes False, the loop stops.

```python
attempts = 0
max_attempts = 3

while attempts < max_attempts:
    password = input("Enter password: ")
    attempts += 1
    print(f"Attempt {attempts} of {max_attempts}")
```

**The for Loop**

When a script needs to process a list of IP addresses, the exact number of items is known in advance. This is the optimal use case for a for loop.

```python
targets = ["192.168.1.1", "192.168.1.2", "192.168.1.3"]

for ip in targets:
    print(f"Scanning {ip}...")
```

The loop variable takes on the value of each element in the list, one at a time. The loop ends when every element has been visited.

**Iterating Over Strings**

Because a string is a sequence, it can be looped through character by character. This is highly useful for password validation.

```python
password = "S3cure!"

for char in password:
    if char.isdigit():
        print(f"Found digit: {char}")
    elif char.isupper():
        print(f"Found uppercase: {char}")
```

**The range() Function**

When a loop needs to run a specific number of times without a predefined list, the `range()` function generates a sequence of numbers.

```python
# range(stop): 0 to stop-1
for i in range(5):
    print(i)       # prints 0, 1, 2, 3, 4

# range(start, stop): start to stop-1
for i in range(1, 6):
    print(i)       # prints 1, 2, 3, 4, 5

# range(start, stop, step): with custom increment
for i in range(0, 20, 5):
    print(i)       # prints 0, 5, 10, 15
```

Because programming counts start from 0, `range(5)` produces five numbers ending at 4.

**Iterating Over Dictionaries**

Dictionaries have `.keys()`, `.values()`, and `.items()` methods. These become especially powerful inside for loops.

```python
services = {22: "SSH", 80: "HTTP", 443: "HTTPS"}

# Loop through both keys and values
for port, name in services.items():
    print(f"Port {port} = {name}")
```

**break and continue**

Two keywords provide fine-grained control inside a loop. The `break` keyword immediately exits the loop entirely.

```python
# Stop scanning as soon as we find port 443
for port in [22, 80, 443, 8080]:
    if port == 443:
        print(f"Port {port} found. Stopping scan.")
        break
    print(f"Checked port {port}")
```

The `continue` keyword skips the rest of the current iteration and moves directly to the next one.

```python
# Skip blank lines when processing a file
lines = ["admin", "", "root", "", "guest"]

for line in lines:
    if line == "":
        continue       # skip empty strings
    print(f"Processing: {line}")
```

A general rule of thumb is to use a for loop when the number of iterations is known in advance. A while loop is best when iterations depend on a changing runtime condition.

Question: What type of loop is best suited for iterating over each item in a list?

> **Answer:** for

Question: What does range(3) produce?

> **Answer:** 0, 1, 2

Question: What keyword immediately exits a loop?

> **Answer:** break

***

## Conclusion

This room covered the data and control flow foundations of Python. Concepts included manipulating strings with built-in methods, understanding data types, and performing type conversion.

The knowledge gained here provides the mechanics to read, understand, and modify Python programs. The next step is learning how to structure those programs into something reusable and resilient.
