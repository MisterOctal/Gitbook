---
icon: python
---

# Python: Simple Demo

Date: 15.03.2026

Room Category: Walkthrough

Core Concept: An introduction to basic Python programming concepts including variables, conditionals, and loops.

As someone aiming to become a top-tier SOC analyst, knowing how to write and read scripts is a huge advantage. Python is the go-to language for automating security tasks, analyzing logs, and writing custom exploits. Getting comfortable with these basics now will make tackling more advanced security tools during my internships easier.

***

### Task 1: Introduction

This room transitions from understanding how computers store data (like in the Data Representation and Data Encoding rooms) to actually telling the computer what to do using Python. Python is a high-level, general-purpose programming language, meaning it's readable for humans and can be used for almost anything, from web apps to automation. The goal of this room is to build a simple "Guess the Number" game.

Here's an example of how the program should be running:

```bash
ubuntu@octal:~$ python guess_the_number.py
I'm thinking of a number between 1 and 20
Take a guess: 10
Too high, try again.
Take a guess: 5
Too low, try again.
Take a guess: 7
Too low, try again.
Take a guess: 8
You got it in 4 tries!
```

Question: Let's code our game!

> Answer: No answer needed

***

### Task 2: Variables

To build our game, we need a way to store information. Variables are like containers for data. We need to store the secret number the computer picks, the user's guess, and the number of attempts. Python's `random` library is used to pick the secret number, and the `print()` function displays text to the user. We also use `input()` to get the user's guess, which is initially read as text (a string) and needs to be converted to a number using `int()`.

Here is the initial draft of the code:

```python
import random # gives us tools for picking random numbers

secret = random.randint(1, 20) # a <= secret <= b
tries = 0
guess = 0 # start with a value that cannot be the secret (since secret is 1..20)

print("I'm thinking of a number between 1 and 20")

text = input("Take a guess: ") # input() returns text (a string)
guess = int(text) # convert the text to a number
tries = tries + 1 # add 1 try
```

This task also offers a VM to run python if you want, but I didn't run it because it wasn't necessary to complete the questions and I didn't wanna wait for a VM to load.

Question: What is the name of the function we used to display text on the screen?

> Answer: print()

Question: What is the name of the function that we used to convert user input to an integer?

> Answer: int()

***

### Task 3: Conditional Statements

Right now, our game just takes a guess and ends. We need logic to compare the user's guess to the secret number and provide feedback. Conditional statements (`if`, `elif`, `else`) allow the program to make decisions based on different scenarios: is the guess out of bounds, too high, too low, or exactly right?

Here is the code with the added conditional logic:

```python
import random # gives us tools for picking random numbers

secret = random.randint(1, 20) # a <= secret <= b
tries = 0
guess = 0 # start with a value that cannot be the secret (since secret is 1..20)

print("I'm thinking of a number between 1 and 20")

text = input("Take a guess: ") # input() returns text (a string)
guess = int(text) # convert the text to a number
tries = tries + 1 # add 1 try

# Give a hint using if / elif / else.
if guess < 1 or guess > 20:
    print("That number is out of range. Try again.")
elif guess < secret:
    print("Too low, try again.")
elif guess > secret:
    print("Too high, try again.")
else:
    print("You got it in", tries, "tries!")
```

Question: How does Python write "else if"?

> Answer: elif

Question: What will the program display if the user's input is 50?

> Answer: That number is out of range. Try again.

***

### Task 4: Iterations

Our game is almost complete, but it only gives the user one try! To make it a real game, we need a way to repeat the guessing process until the user gets it right. This is where iterations, specifically `while` loops, come in. A `while` loop will keep running a block of code as long as a specific condition remains true. In our case, the condition is `guess != secret` (while the guess does not equal the secret number).

Here is the final, working game code:

```python
import random # gives us tools for picking random numbers

# ----------------------------
# Guess the Number (Beginner Demo)
# ----------------------------
# The computer picks a secret number.
# The player keeps guessing until they find it.

secret = random.randint(1, 20) # a <= secret <= b
tries = 0
guess = 0 # start with a value that cannot be the secret (since secret is 1..20)

print("I'm thinking of a number between 1 and 20")

# Repeat until the user guesses the secret number.
while guess != secret:
    text = input("Take a guess: ") # input() returns text (a string)
    guess = int(text) # convert the text to a number
    tries = tries + 1 # add 1 try

    # Give a hint using if / elif / else.
    if guess < 1 or guess > 20:
        print("That number is out of range. Try again.")
    elif guess < secret:
        print("Too low, try again.")
    elif guess > secret:
        print("Too high, try again.")
    else:
        print("You got it in", tries, "tries!")
```

Question: What type of loop does this program use?

> Answer: while

Question: What will the program display if the user makes the correct guess in 3 tries?

> Answer: You got it in 3 tries!

***

### Conclusion

This room was a fantastic introduction to the core pillars of programming: variables, conditionals, and loops. Building a simple "Guess the Number" game is the perfect way to see how these concepts work together. Next up is exploring how these same concepts look in JavaScript!
