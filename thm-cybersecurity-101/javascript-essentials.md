---
icon: js
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/66c513e4445cb5649e636a36/room-content/66c513e4445cb5649e636a36-1725908825341.png
coverY: 0
coverHeight: 139
---

# JavaScript Essentials

**Date:** 30.03.2026&#x20;

**Room Category:** Walkthrough

**Core Concept:** Understanding the "Engine of Interactivity." This guide covers how client-side scripting powers the modern web and why its transparency to the user makes it a unique attack vector for security researchers.

While HTML provides the skeleton and CSS the skin, JavaScript (JS) is the muscle and nervous system of a website. Because JS runs directly in the user's browser (client-side), it is completely transparent. As a security researcher, being able to read, deobfuscate, and manipulate JS is the key to finding vulnerabilities like XSS, CSRF, and logic bypasses.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (9) (1).png" alt="" width="312"><figcaption></figcaption></figure>

JavaScript (JS) is a popular scripting language that allows web developers to add interactive features to websites containing HTML and CSS. Once the HTML elements are created, you can add interactiveness like validation, onClick actions, and animations through JS.

Learning the language is equally important as learning HTML and CSS. This room is an introductory overview tailored for beginners, focusing on fundamentals from a cyber perspective and how hackers utilize legitimate functionalities for malicious results.

**Learning Objectives:**

* Understand the basics of JS.
* Integrating JS in HTML.
* Abusing Dialogue Functions.
* Bypassing Control Flow Statements.
* Exploring Minified Files.

Question: I have successfully started the attached VM.

> **Answer:** No answer needed

***

## Task 2: Essential Concepts

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Before writing code, it is vital to understand the "buckets" and "instructions" used in JS.

#### Variables

Variables are containers for storing data values. In JS, each variable has a name to reference it later. There are three ways to declare variables:

* **var:** Function-scoped.
* **let:** Block-scoped.
* **const:** Block-scoped (cannot be reassigned).

#### Data Types

Data types define the type of value a variable can hold, such as **string** (text), **number**, **boolean** (true/false), **null**, **undefined**, and **object**.

#### Functions

A function is a block of code designed to perform a specific task.

```
function PrintResult(rollNum) {
    alert("Username with roll number " + rollNum + " has passed the exam");
}
```

#### Loops

Loops allow you to run a code block multiple times as long as a condition is true. Common loops include `for`, `while`, and `do...while`.

```javascript
const rollNumbers = [101, 102, 103];
for (let i = 0; i < 3; i++) {
    PrintResult(rollNumbers[i]);
}
```

Question: What term allows you to run a code block multiple times as long as it is a condition?

> **Answer:** Loop

***

## Task 3: JavaScript Overview

JS is an **interpreted** language. The browser reads it line-by-line and executes it on the fly.

* **Key Tool:** The **Browser Console** (Ctrl+Shift+I). This is a real-time playground for testing code and inspecting logic.

**Example Code:**

```javascript
let x = 5;
let y = 10;
let result = x + y;
console.log("The result is: " + result);
```

Question: What is the code output if the value of x is changed to 10?

> **Answer:** The result is: 20

Question: Is JavaScript a compiled or interpreted language?

> **Answer:** Interpreted

***

## Task 4: Integrating JavaScript in HTML

There are two primary ways to load JS into a page:

1. **Internal:** Code is wrapped in `<script>` tags inside the HTML file.
2. **External:** Code lives in a separate `.js` file and is linked via `<script src="script.js"></script>`.

Question: Which type of JavaScript integration places the code directly within the HTML document?

> **Answer:** Internal

Question: Which method is better for reusing JS across multiple web pages?

> **Answer:** External

Question: What is the name of the external JS file that is being called by external\_test.html?

> **Answer:** thm\_external.js

Question: What attribute links an external JS file in the \<script> tag?

> **Answer:** src

***

## Task 5: Abusing Dialogue Functions

JS has three main "Dialogue" functions that pop up boxes. These are often used for XSS (Cross-Site Scripting) proofs of concept:

* **alert():** Displays information (one button: OK).
* **prompt():** Requests input (returns a string).
* **confirm():** Requests verification (returns true or false).

**Abuse Example (Alert Bombing):**

```javascript
<script>
    for (let i = 0; i < 500; i++) {
        alert("Hacked");
    }
</script>
```

Question: In the file invoice.html, how many times does the code show the alert Hacked?

> **Answer:** 5

Question: Which of the JS interactive elements should be used to display a dialogue box that asks the user for input?

> **Answer:** prompt

Question: If the user enters Tesla, what value is stored in the carName variable?

> **Answer:** Tesla

***

## Task 6: Bypassing Control Flow Statements

Control flow (`if/else`, `switch`, `loops`) determines the logic path. **Security Warning:** Never put sensitive logic (like a login check) only in client-side JS. Since the user owns the browser, they can see the required credentials in the source code.

Question: What is the message displayed if you enter the age less than 18?

> **Answer:** You are a minor.

Question: What is the password for the user admin (found in login.html source)?

> **Answer:** ComplexPassword

***

## Task 7: Exploring Minified Files

To save bandwidth, developers use **Minification** (removing spaces/comments) and **Obfuscation** (changing names to gibberish).

* **Minified:** `function hi(){alert("Welcome to THM");}hi();`
* **Obfuscated:** Uses complex hex strings like `_0x114713`.

Question: What is the alert message shown after running the file hello.html?

> **Answer:** Welcome to THM

Question: What is the value of the age variable in the obfuscated snippet `age=0x1*0x247e+0x35*-0x2e+-0x1ae3;`?

> **Answer:** 21

***

## Task 8: Best Practices

1. **Server-Side Validation:** Never trust the browser. JS validation is for user experience, but the server must validate for security.
2. **Trusted Libraries:** Check the integrity of third-party scripts.
3. **No Hardcoded Secrets:** Never put API keys or passwords in JS files.
4. **Minify for Production:** It adds a layer of "Security through Obscurity" that slows down attackers.

Question: Is it a good practice to blindly include JS in your code from any source (yea/nay)?

> **Answer:** nay

***

## Conclusion

JavaScript's greatest strength is its accessibility and execution in the browser, but this is also its greatest security weakness. Learning to navigate the console, understand the difference between internal and external scripts, and identify obfuscated code provides a solid foundation for web penetration testing. Remember that "client-side" means "user-controlled."
