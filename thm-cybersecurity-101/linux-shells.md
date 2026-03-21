---
icon: terminal
---

# Linux Shells

**Date:** 21.03.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Understanding the interface between the user and the Linux kernel, including different shell types and basic scripting.

A shell is a program that takes commands from the keyboard and gives them to the operating system to perform. In the early days of computing, it was the only user interface available on a Unix-like system. Today, it remains a powerful tool for systems administration, server management, and security auditing. Mastering the shell is a fundamental requirement for security professionals to interact with the underlying OS efficiently.

***

## Task 1: Introduction to Linux Shells

This task explores the relationship between the user and the operating system. It highlights the distinction between the **Shell** (the command interpreter) and the **Terminal** (the graphical window or wrapper that displays the shell). For security analysis, the shell provides the environment necessary to execute specialized tools like Nmap, Metasploit, or custom automation scripts.

Question: Who is the facilitator between the user and the OS?

> **Answer:** Shell

***

## Task 2: How To Interact With a Shell?

Interaction with a shell involves entering a command and pressing enter. The shell then searches for that command within the system's `PATH`—a predefined list of directories containing executable programs—executes it, and returns the output. This CLI-based interaction is often more resource-friendly and faster than using a Graphical User Interface (GUI).

Common Commands for Analysis:

* `pwd`: Print Working Directory. Displays the current location within the file structure.
* `cd`: Change Directory. Navigates between different system areas.
* `ls`: List Directory Contents. Lists files; using `ls -la` reveals hidden files like `.bash_history`.
* `cat`: Display File Contents. Reads the text inside files without opening an editor.
* `grep`: Global Regular Expression Print. Searches through files or command output for specific patterns, such as identifying "failed password" attempts in system logs.

Question: What is the default shell in most Linux distributions?

> **Answer:** Bash

Question: Which command utility is used to list down the contents of a directory?

> **Answer:** ls

Question: Which command utility can help you search for anything in a file?

> **Answer:** grep

***

## Task 3: Types of Linux Shells

Linux distributions offer several shell environments. The current active shell can be identified using `echo $SHELL`, and a list of all valid login shells is stored in `/etc/shells`.

* **Bash (Bourne Again Shell):** The industry standard found on most Linux servers, known for its compatibility and robust scripting capabilities.
* **Fish (Friendly Interactive Shell):** Designed for user-friendliness, offering out-of-the-box features like syntax highlighting and auto-suggestions.
* **Zsh (Z Shell):** Highly customizable and often used as the default in modern environments (like macOS) due to its extensive plugin support.

Question: Which shell comes with syntax highlighting as an out-of-the-box feature?

> **Answer:** Fish

Question: Which shell does not have auto spell correction?

> **Answer:** Bash

Question: Which command displays all the previously executed commands of the current session?

> **Answer:** history

***

## Task 4: Shell Scripting and Components

Shell scripting automates repetitive tasks by saving a sequence of commands into a `.sh` file. This capability is central to creating exploit code, automated scanners, or post-exploitation tools.

Key Components:

1. **Shebang (`#!`):** The first line of a script that directs the kernel to the correct interpreter (e.g., `#!/bin/bash`).
2. **Variables:** Data containers used to store and reuse information like IP addresses or file paths.
3. **Loops:** Functionality used to repeat actions, such as iterating through a list of targets.
4. **Conditionals:** Logic gates (`if`, `elif`, `else`) that execute code based on whether specific conditions are met.

Loop Implementation Example:

```bash
#!/bin/bash
# Iterates and prints numbers 1 through 10
for i in {1..10}; do
    echo $i
done
```

Conditional Logic Example:

```bash
#!/bin/bash
# Simulates a basic authentication check
echo "Please enter your name:"
read name
if [ "$name" = "Stewart" ]; then
    echo "Welcome Stewart! Here is the secret: THM_Script"
else
    echo "Access Denied."
fi
```

Question: What is the shebang used in a Bash script?

> **Answer:** #!/bin/bash

Question: Which command gives executable permissions to a script?

> **Answer:** chmod +x

Question: Which scripting functionality helps us configure iterative tasks?

> **Answer:** Loops

***

## Task 5: The Locker Script

The "Locker Script" demonstrates a practical application of combined scripting elements. It uses a `for` loop to collect three pieces of user input (Username, Company, and PIN) and employs logical "AND" (`&&`) operators within a conditional statement to validate all credentials simultaneously. Analyzing such scripts is useful for understanding how malicious installers or logic bombs might operate.

Locker Script Logic:

```bash
#!/bin/bash 
username=""
companyname=""
pin=""

# Loop runs 3 times to gather required credentials
for i in {1..3}; do
    if [ "$i" -eq 1 ]; then
        echo "Enter your Username:"
        read username
    elif [ "$i" -eq 2 ]; then
        echo "Enter your Company name:"
        read companyname
    else
        echo "Enter your PIN:"
        read pin
    fi
done

# Final multi-variable validation
if [ "$username" = "John" ] && [ "$companyname" = "Tryhackme" ] && [ "$pin" = "7385" ]; then
    echo "Authentication Successful. You can now access your locker, John."
else
    echo "Authentication Denied!!"
fi
```

Question: What would be the correct PIN to authenticate in the locker script?

> **Answer:** 7385

***

## Task 6: Practical Exercise

This exercise simulates searching for a specific string (a "flag") buried within system logs. In a Security Operations Center (SOC) environment, automation is used to parse files like `/var/log/auth.log` for indicators of compromise (IoC) such as specific IP addresses or unauthorized access attempts.

**Flag Hunt Script Analysis:** The script iterates through all `.log` files in the `/var/log` directory. It uses `grep -q` to perform a "quiet" search, returning a success or failure status without printing the file contents to the terminal.

```bash
#!/bin/bash
directory="/var/log"
flag="thm-flag01-script"

echo "Flag search in directory: $directory in progress..."

for file in "$directory"/*.log; do
    # -q checks for existence without displaying match data
    if grep -q "$flag" "$file"; then
        echo "Flag found in: $(basename "$file")"
    fi
done
```

Question: Which file has the keyword?

> **Answer:** authentication.log

Question: Where is the cat sleeping?

> **Answer:** under the table

***

## Conclusion

Understanding Linux Shells is a fundamental skill for security certifications and advanced technical studies. Whether automating server maintenance or conducting a penetration test, the shell serves as the primary interface for system interaction. Mastery of these concepts allows for a deeper understanding of how systems process instructions and data.
