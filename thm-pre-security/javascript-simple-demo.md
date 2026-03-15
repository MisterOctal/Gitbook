---
icon: js
---

# JavaScript: Simple Demo

Date: 15.03.2026

Room Category: Walkthrough

Core Concept: An introduction to basic JavaScript programming concepts including variables, conditionals, and loops.

Knowing how to read and write JavaScript is incredibly useful for any security analyst. Since JS is the native language of web browsers, having a solid grasp of how it functions is a hard requirement for finding client side vulnerabilities like Cross Site Scripting (XSS). Getting comfortable with these coding basics now will definitely pay off when I start analyzing malicious web payloads down the line.

***

### Task 1: Introduction

This room dives into JavaScript, which is arguably the most widely used programming language in the world thanks to its integration into every modern web browser. While it was originally built just to make websites interactive, environments like Node.js now allow it to run on backend servers too. For this room, we are focusing on building a "Guess the Number" game to see how JavaScript handles basic logic.

Below is an example of how the program should run:

```bash
ubuntu@tryhackme:~/JavaScript-Demo$ node guess_v3.js
I'm thinking of a number between 1 and 20
Take a guess: 10
Too high, try again.
Take a guess: 5
Too low, try again.
Take a guess: 7
You got it in 3 tries!
```

Question: Let's write our game in JavaScript!

> Answer: No answer needed

***

### Task 2: Variables

Whenever you write a script, you need a place to store data in memory. In JavaScript, we declare these storage containers using keywords like `let` or `const`. Since the player's guess and the attempt counter will change as the game progresses, `let` is the perfect choice.&#x20;

To make the game work, we rely on built in mathematical functions like `Math.random()` to generate the secret number and `Math.floor()` to round it down. We also use `prompt()` to grab the user's input from the browser window and `parseInt()` to ensure that input is treated as a number instead of text.

Here is the initial draft of the code:

```javascript
// ----------------------------
// Guess the Number (JavaScript Demo)
// ----------------------------
// The computer picks a secret number.
let secret = Math.floor(Math.random() * 20) + 1;
let tries = 0;
let guess = 0; // start with a value that cannot be the secret
console.log("I'm thinking of a number between 1 and 20");
let text = prompt("Take a guess: "); // prompt() returns text (a string)
guess = parseInt(text); // convert the text to a number
tries = tries + 1; // add 1 try
```

Question: What word is used to declare a variable?

> Answer: let

Question: What word is used to declare a constant?

> Answer: const

Question: What is the name of the function we used to display text on the screen?

> Answer: console.log()

***

### Task 3: Prompting the User for Input

In order to make the game interactive, we need a way to accept the user's guesses. Because we are running this script in Node.js rather than directly in a web browser, we have to import a specific module called `readline` to pause the script and wait for the user to type something. Once the user types their guess, it is received as text. We then use the `parseInt()` method to convert that text string into a usable base 10 integer.

Here is the code with the added user input:

```javascript
import * as readline from "node:readline/promises";
import { stdin as input, stdout as output } from "node:process";

const rl = readline.createInterface({ input, output });

try {
    const secret = Math.floor(Math.random() * (20)) + 1; // 1 <= secret <= 20
    let tries = 0;
    let guess = 0; // start with a value that cannot be the secret (since secret is 1..20)

    console.log("I'm thinking of a number between 1 and 20");

    const text = await rl.question("Take a guess: "); // rl.question() returns text (a string)
    guess = parseInt(text, 10); // convert the text to a number

    tries = tries + 1; // add 1 try

} finally {
    rl.close();
}
```

Question: What method is used to convert user input into a number?

> Answer: parseInt()

***

### Task 4: Conditional Statements

A game is not very fun if it cannot make decisions. To evaluate whether the player's guess is too high, too low, or completely out of bounds, we use conditional statements. JavaScript relies heavily on curly braces `{}` to define blocks of code and parentheses `()` to hold the actual conditions being evaluated. We also use logical operators like the double pipe `||`, which represents "or", to check multiple scenarios at once.

Here is the code witht he added conditional statements:

```javascript
import * as readline from "node:readline/promises";
import { stdin as input, stdout as output } from "node:process";

const rl = readline.createInterface({ input, output });

try {
    const secret =
        Math.floor(Math.random() * (20)) + 1; // 1 <= secret <= 20
    let tries = 0;
    let guess = 0; // start with a value that cannot be the secret (since secret is 1..20)

    console.log("I'm thinking of a number between 1 and 20");

    const text = await rl.question("Take a guess: "); // rl.question() returns text (a string)
    guess = parseInt(text, 10); // convert the text to a number

    tries = tries + 1; // add 1 try

    // Give a hint using if / else if / else.
    if (guess < 1 || guess > 20) {
        console.log("That number is out of range. Try again.");
    } else if (guess < secret) {
        console.log("Too low, try again.");
    } else if (guess > secret) {
        console.log("Too high, try again.");
    } else {
        console.log("You got it in", tries, "tries!");
    }
} finally {
    rl.close();
}
```

Question: The secret is 10 . What will our program display on the screen if the user makes a guess of 15?

> Answer: Too high, try again.

Question: The secret is 10 . What will our program display on the screen if the user makes a guess of 35?

> Answer: That number is out of range. Try again.

***

### Task 5: Iterations

If we stop here, the player only gets a single attempt to guess the number. To turn this into a continuous game, we wrap our logic inside a `while` loop. This tells the script to keep executing the code block repeatedly as long as a specific condition remains true. In our script, the loop keeps prompting the user and incrementing the `tries` variable until their guess matches the secret number. Pay close attention to the strict inequality operator `!==` which ensures both the value and the data type do not match.

Here is the final script now with iterations added:

```javascript
import * as readline from "node:readline/promises";
import { stdin as input, stdout as output } from "node:process";

const rl = readline.createInterface({ input, output });

try {
    const secret =
        Math.floor(Math.random() * (20)) + 1; // 1 <= secret <= 20
    let tries = 0;
    let guess = 0; // start with a value that cannot be the secret (since secret is 1..20)

    console.log("I'm thinking of a number between 1 and 20");

    // Repeat until the user guesses the secret number.
    while (guess !== secret) {
        const text = await rl.question("Take a guess: "); // rl.question() returns text (a string)
        guess = parseInt(text, 10); // convert the text to a number

        tries = tries + 1; // add 1 try

        // Give a hint using if / else if / else.
        if (guess < 1 || guess > 20) {
            console.log("That number is out of range. Try again.");
        } else if (guess < secret) {
            console.log("Too low, try again.");
        } else if (guess > secret) {
            console.log("Too high, try again.");
        } else {
            console.log("You got it in", tries, "tries!");
        }
    }
} finally {
    rl.close();
}
```

Question: What is the name of the loop that we used in this task?

> Answer: while

Question: What is the name of the variable that is incremented by one when the user makes a new wrong guess?

> Answer: tries

Question: How is "not equal" written in JavaScript?

> Answer: !==

***

### Conclusion

This room was a fantastic introduction to the building blocks of JavaScript. Variables, conditionals, and loops form the core of almost every script on the web. Getting familiar with JavaScript's specific syntax, from its heavy use of curly braces to its strict equality operators, is a major stepping stone toward reading and understanding complex web application logic.
