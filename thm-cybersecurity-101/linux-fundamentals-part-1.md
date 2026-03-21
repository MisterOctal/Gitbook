---
icon: linux
---

# Linux Fundamentals Part 1

**Date:** 17.03.2026

**Room Category:** Walkthrough

**Core Concept:** An introduction to the Linux operating system, basic terminal commands, navigating the filesystem, finding files, and using shell operators.

Linux is the backbone of modern computing and cybersecurity. From enterprise web servers to embedded industrial devices, Linux powers the vast majority of the internet. Because penetration testing tools and server environments are heavily Linux-based, becoming comfortable with the command-line interface is a mandatory skill for any security professional. This room serves as the perfect starting point for learning how to interact with the system entirely through text commands.

***

## Task 1: Introduction

This room is the first in a three-part series covering Linux fundamentals. It introduces the absolute basics, from understanding the history of the operating system to running your very first commands in an interactive terminal. Operating systems like Ubuntu and Debian are highly customizable and extremely lightweight, making them the standard choice for servers and security tasks.

Question: Let's get started!

> **Answer:** No answer needed

***

## Task 2: A Bit of Background on Linux

Linux is an open-source operating system based on Unix. Because it is open-source, it comes in many different "flavors" or distributions (like Ubuntu, Debian, or Alpine) designed for specific purposes. It is incredibly versatile, running everything from smart car entertainment panels to critical infrastructure and checkout registers.

Question: What year was the first release of a Linux operating system?

> **Answer:** 1991

***

## Task 3: Interacting With Your First Linux Machine

To practice these commands, this task simply requires you to deploy the interactive Ubuntu Linux machine provided in the browser. You interact with it using an IP address, which acts as the machine's street address on the network.

Question: I've deployed my first Linux machine!

> **Answer:** No answer needed

***

## Task 4: Running Your First few Commands

When interacting with a server, there is often no graphical user interface (GUI). You must rely on the terminal. The two commands introduced here are `echo` (which outputs or repeats text back to the terminal) and `whoami` (which tells you the username of the account you are currently logged in as).

```bash
# Output text to the terminal
echo TryHackMe

# Find out the current logged-in user
whoami
```

Question: If we wanted to output the text "TryHackMe", what would our command be?

> **Answer:** echo TryHackMe

Question: What is the username of who you're logged in as on your deployed Linux machine?

> **Answer:** tryhackme

***

## Task 5: Interacting With the Filesystem!

Navigating the filesystem via the command line requires a few essential commands. `ls` (list) shows you the files and folders in your current location. `cd` (change directory) allows you to move into those folders. `cat` (concatenate) outputs the contents of a file directly to the screen. Finally, `pwd` (print working directory) tells you your exact absolute path on the system so you do not get lost.

```bash
# List files and directories
ls

# Move into a specific directory
cd folder4

# Print your current absolute path
pwd

# Read the contents of a file
cat note.txt
```

Question: On the Linux machine that you deploy, how many folders are there?

> **Answer:** 4

Question: Which directory contains a file?

> **Answer:** folder4

Question: What is the contents of this file?

> **Answer:** Hello World

Question: Use the cd command to navigate to this file and find out the new current working directory. What is the path?

> **Answer:** /home/tryhackme/folder4

***

## Task 6: Searching for Files

Navigating folder by folder using `cd` and `ls` is highly inefficient when looking for a specific file. The `find` command automates this by searching directories for files that match a specific name or extension. Alternatively, the `grep` command searches inside the actual contents of files for specific text strings, which is incredibly useful for filtering massive log files to find specific IP addresses or error codes.

```bash
# Find all files with a .txt extension
find . -name "*.txt"

# Search inside access.log for lines containing "THM"
grep "THM" access.log
```

Question: Use grep on "access.log" to find the flag that has a prefix of "THM". What is the flag? **Note:** The "access.log" file is located in the "/home/tryhackme/" directory.

> **Answer:** THM{ACCESS}

Question: And I still haven't found what I'm looking for!

> **Answer:** No answer needed

***

## Task 7: An Introduction to Shell Operators

Shell operators allow you to chain commands together or control their output. The ampersand `&` allows a command to run in the background, freeing up your terminal. Output redirectors are used to send the results of a command into a file instead of printing it to the screen. A single greater-than sign `>` overwrites the destination file, while a double greater-than sign `>>` appends the output to the bottom of the file without deleting existing content.

```bash
# Run a process in the background
sleep 60 &

# Overwrite a file with new output
echo password123 > passwords

# Append output to the end of an existing file
echo tryhackme >> passwords
```

Question: If we wanted to run a command in the background, what operator would we want to use?

> **Answer:** &

Question: If I wanted to replace the contents of a file named "passwords" with the word "password123", what would my command be?

> **Answer:** echo password123 > passwords

Question: Now if I wanted to add "tryhackme" to this file (named passwords) but also keep "passwords123", what would my command be?

> **Answer:** echo tryhackme >> passwords

Question: Now use the deployed Linux machine to put these into practice

> **Answer:** No answer needed

***

## Conclusion

This room provided a comprehensive first look at the Linux command line. We covered the essential commands needed to navigate the filesystem, search for specific files or text, and control output using shell operators. These fundamentals form the exact foundation required to confidently operate servers, run security tools, and transition into the next stages of Linux training.
