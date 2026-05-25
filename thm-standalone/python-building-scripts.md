---
icon: python
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/5f04259cf9bf5b57aed2c476-1779122607085
coverY: 0
coverHeight: 138
---

# Python: Building Scripts

**Date:** 25.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Functions, error handling, file I/O, libraries, and building complete Python scripts.

Four main capabilities are covered in this room. These include functions for organizing logic, error handling for building resilience, file I/O for reading input and writing output, and libraries for leveraging existing code. The final objective involves combining these concepts into a complete Password Strength Checker.

## Task 1: Introduction

This room highlights the importance of reusability, safety, and leveraging the Python ecosystem to solve practical problems. The final tool will evaluate passwords against length requirements, character variety rules, and a common-passwords wordlist.

Question: Let's build real scripts!

> **Answer:** No answer needed

***

## Task 2: Functions

As programs grow larger, writing the same logic in multiple places becomes error-prone and tedious. Functions solve this problem by providing reusable blocks of code. A function takes input, performs a task, and optionally returns a result.

In Python, functions are defined using the `def` keyword, followed by the function name, parentheses for parameters, and a colon. The indented code block beneath is the function body.

```python
def greet(name):
    print(f"Hello, {name}. Welcome to the system.")
```

To use or call this function, the name and arguments are provided:

```python
greet("Alice")    # Hello, Alice. Welcome to the system.
greet("Bob")      # Hello, Bob. Welcome to the system.
```

A parameter is the variable name in the function definition, while an argument is the actual value passed during a function call. Functions can send a result back to the caller using the `return` keyword.

```python
def check_length(password, min_length):
    if len(password) >= min_length:
        return True
    else:
        return False

result = check_length("Tr0ub4dor", 8)
print(result)   # True
```

If a function lacks a return statement, it returns `None` by default. Functions can accept multiple parameters separated by commas.

```python
def score_password(password, common_list):
    score = 0

    if len(password) >= 8:
        score += 1
    if len(password) >= 12:
        score += 1
    if any(c.isdigit() for c in password):
        score += 1
    if any(c.isupper() for c in password):
        score += 1
    if password not in common_list:
        score += 1

    return score
```

Default parameter values can be assigned so the caller does not have to supply them every time.

```python
def check_length(password, min_length=8):
    return len(password) >= min_length

print(check_length("short"))          # False   (uses default min_length of 8)
print(check_length("short", 4))       # True    (overrides default with 4)
```

Scope dictates that variables created inside a function exist only within that function. They are not accessible from the outside, preventing accidental data interference across different parts of a program.

```python
def calculate():
    result = 42     # local variable
    return result

calculate()
# print(result)    # This would cause an error: result is not defined here
```

Question: What keyword sends a value back from a function to the caller?

> **Answer:** return

Question: If a function is defined as `def scan(target, port=80):`, what value does port take if you call `scan("192.168.1.1")` without specifying a port?

> **Answer:** 80

Question: On the VM, run functions\_demo.py. What score does score\_password return for the password "TryHackMe2025!"?

> **Answer:** 5

```bash
python3 functions_demo.py
```

***

## Task 3: Error Handling

Supplying text when a program expects a number can cause crashes. For example, a `ValueError` occurs during integer conversion of text.

```python
text = input("Enter a number: ")   # user types "hello"
number = int(text)                  # CRASH: ValueError
print(f"You entered {number}")
```

A polished program requires error handling to provide helpful messages instead of crashing entirely. Python utilizes the `try/except` structure to attempt a risky operation and catch any resulting errors.

```python
try:
    text = input("Enter a number: ")
    number = int(text)
    print(f"You entered {number}")
except ValueError:
    print("That is not a valid number. Please try again.")
```

If an error occurs within the `try` block, Python jumps to the matching `except` block and executes its logic. Common exception types include `ValueError`, `FileNotFoundError`, `ZeroDivisionError`, `KeyError`, and `IndexError`.

Multiple exceptions can be caught explicitly.

```python
try:
    filename = input("File to open: ")
    with open(filename) as f:
        data = f.read()
    port = int(data.strip())
except FileNotFoundError:
    print(f"Error: '{filename}' does not exist.")
except ValueError:
    print(f"Error: the file does not contain a valid number.")
```

A generic `except Exception as e` can be used to catch anything, though it should be used sparingly as it might hide other bugs.

```python
try:
    risky_operation()
except Exception as e:
    print(f"Something went wrong: {e}")
```

Error handling is frequently combined with `while` loops. This allows the program to continuously prompt for input until valid data is provided by the user.

```python
while True:
    try:
        age = int(input("Enter your age: "))
        break   # valid input received; exit the loop
    except ValueError:
        print("Invalid input. Please enter a whole number.")
```

Question: What type of exception is raised when you attempt `int("hello")`?

> **Answer:** ValueError

Question: What type of exception is raised when you try to open a file that does not exist?

> **Answer:** FileNotFoundError

Question: On the VM, open a Python terminal and type `print(10 / 0)`. What is the name of the exception that Python raises?

> **Answer:** ZeroDivisionError

```bash
python3 -c "print(10/0)"
```

***

## Task 4: Reading and Writing Files

File operations are essential for tasks like reading wordlists, parsing log files, and writing scan results. Python uses the built-in `open()` function with specific modes. The modes include `"r"` for reading, `"w"` for writing, and `"a"` for appending.

```python
f = open("passwords.txt", "r")
content = f.read()
print(content)
f.close()
```

To prevent issues with leaked file handles, modern Python utilizes the `with` statement as a context manager. This ensures the file automatically closes when the indented block ends.

```python
with open("passwords.txt", "r") as f:
    content = f.read()

print(content)   # the file is already closed at this point
```

Methods for reading include `.read()` for the entire file, `.readline()` for a single line, and `.readlines()` for a list of lines. A memory-efficient approach involves looping directly over the file object line by line. Using the `.strip()` method is crucial here to remove trailing newline characters from each read string.

```python
with open("common_passwords.txt", "r") as f:
    for line in f:
        password = line.strip()    # remove the trailing newline
        print(password)
```

Wordlists can be loaded directly into a list for future checks.

```python
common_passwords = []

with open("common_passwords.txt", "r") as f:
    for line in f:
        common_passwords.append(line.strip())

print(f"Loaded {len(common_passwords)} common passwords.")
```

The loaded list can then be utilized with the `in` operator.

```python
if user_password in common_passwords:
    print("This password appears in the common-passwords list.")
```

Writing to a file uses `"w"` mode to overwrite or create a file.

```python
with open("results.txt", "w") as f:
    f.write("Scan Results\n")
    f.write("============\n")
    f.write(f"Target: 192.168.1.1\n")
    f.write(f"Open ports: 22, 80, 443\n")
```

The `"a"` mode appends data to the end without erasing the existing content.

```python
with open("results.txt", "a") as f:
    f.write(f"Additional finding: port 3306 open\n")
```

Question: What keyword introduces a context manager for safely opening files?

> **Answer:** with

Question: What string method removes the trailing newline character from each line read from a file?

> **Answer:** .strip()

Question: On the VM, run files\_demo.py. How many passwords did it load from common\_passwords.txt?

> **Answer:** 58

```bash
python3 files_demo.py
```

***

## Task 5: Libraries and Pip

A library, often referred to as a module or package, is a collection of pre-written code that can be imported into custom programs. Python offers various ways to import libraries.

```python
# Import the entire library
import os
print(os.getcwd())          # prints the current working directory

# Import a specific function
from datetime import datetime
now = datetime.now()
print(f"Current time: {now}")

# Import with an alias (nickname)
import datetime as dt
now = dt.datetime.now()
```

The Python Standard Library ships with built-in modules like `os`, `sys`, `random`, `datetime`, `json`, `hashlib`, and `string`. For example, the `string` module provides handy constants for character variety checks.

```python
import string

password = "S3cure!Pass"

has_upper = any(c in string.ascii_uppercase for c in password)
has_digit = any(c in string.digits for c in password)
has_special = any(c in string.punctuation for c in password)

print(f"Uppercase: {has_upper}")     # True
print(f"Digit: {has_digit}")         # True
print(f"Special: {has_special}")     # True
```

Third-party libraries are installed via the Python Package Index (PyPI) using `pip`, the default package manager.

```bash
pip install requests
```

Once installed, they are imported normally. Security-relevant libraries often encountered include `requests`, `scapy`, `pwntools`, `paramiko`, and `beautifulsoup4`.

```python
import requests

response = requests.get("[https://tryhackme.com](https://tryhackme.com)")
print(response.status_code)   # 200
```

Question: What is the name of Python's package manager used to install third-party libraries?

> **Answer:** pip

Question: Which module from the standard library provides constants like ascii\_uppercase, digits, and punctuation?

> **Answer:** string

Question: On the VM, run imports\_demo.py. What is the first character of the SHA-256 hash it prints for the default input string?

> **Answer:** 5

```bash
python3 imports_demo.py
```

***

## Task 6: Putting It All Together: Password Strength Checker

The final task combines all previous concepts into a working Password Strength Checker. The program loads a list of common passwords, prompts the user for a password, and checks its length and character variety.

The first step involves importing modules and defining the wordlist loading function. The `try/except` block ensures the program continues even if the file is missing.

```python
import string

def load_common_passwords(filepath):
    """Load a list of common passwords from a text file."""
    common = []
    try:
        with open(filepath, "r") as f:
            for line in f:
                common.append(line.strip().lower())
    except FileNotFoundError:
        print(f"Warning: '{filepath}' not found. Skipping common-password check.")
    return common
```

The scoring function evaluates various conditions and returns a numeric score alongside a list of improvement suggestions.

```python
def check_password(password, common_list):
    """Evaluate a password and return (score, feedback_list)."""
    score = 0
    feedback = []

    # Length checks
    if len(password) >= 8:
        score += 1
    else:
        feedback.append("Password should be at least 8 characters.")

    if len(password) >= 12:
        score += 1

    # Character variety checks
    if any(c in string.ascii_uppercase for c in password):
        score += 1
    else:
        feedback.append("Add at least one uppercase letter.")

    if any(c in string.digits for c in password):
        score += 1
    else:
        feedback.append("Add at least one digit.")

    if any(c in string.punctuation for c in password):
        score += 1
    else:
        feedback.append("Add at least one special character (e.g., !, @, #).")

    # Common password check (overrides all other scoring)
    if password.lower() in common_list:
        score = 0
        feedback = ["This password is in the common-passwords list. Choose another."]

    return score, feedback
```

The main program loop handles user input and maps numerical scores to readable strength labels. It logs the results securely by replacing the plaintext password characters with asterisks before writing to the log file.

```python
def main():
    strength_labels = {
        0: "Weak", 1: "Weak",
        2: "Moderate", 3: "Moderate",
        4: "Strong", 5: "Strong"
    }

    common_list = load_common_passwords("common_passwords.txt")

    while True:
        password = input("\nEnter a password to check (or 'quit' to exit): ")

        if password.lower() == "quit":
            print("Goodbye.")
            break

        if len(password) == 0:
            print("Password cannot be empty. Try again.")
            continue

        score, feedback = check_password(password, common_list)
        label = strength_labels.get(score, "Unknown")

        print(f"\nStrength: {label} ({score}/5)")

        if feedback:
            print("Suggestions:")
            for tip in feedback:
                print(f"  - {tip}")

        # Log the result (mask the actual password with asterisks)
        with open("password_log.txt", "a") as log:
            log.write(f"Password: {'*' * len(password)} | Strength: {label} ({score}/5)\n")

main()
```

Question: In the check\_password function, what score does the password receive if it is found in the common\_passwords list?

> **Answer:** 0

Question: On the VM, run password\_checker.py and enter the password TryHackMe!2025. What strength label does the program display?

> **Answer:** Strong

```bash
python3 password_checker.py
```

Question: After running the checker with the password TryHackMe!2025, open password\_log.txt on the VM. How many asterisks appear on that line?

> **Answer:** 14

```bash
cat password_log.txt
```

***

## Conclusion

Functions provide reusable code blocks, error handling prevents crashes on unexpected input, and file I/O allows safe data processing. Libraries extend functionality by offering access to standard and third-party modules. These integrated skills form the foundation necessary to read, modify, and write Python programs for practical security applications.
