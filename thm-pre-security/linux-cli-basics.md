---
icon: linux
---

# Linux CLI Basics

Date: 08.03.2026

Room Category: Walkthrough

Core Concept: Navigating the Linux file system and gathering system information via the terminal.

Linux is everywhere in cybersecurity. It powers the servers, the security tools, and the environments we have to defend. Before I can start doing deep dives into logs or investigating incidents, I need to be completely comfortable navigating the OS using the Command Line Interface (CLI). This room puts us in the shoes of a new intern getting familiar with the terminal, which is the exact baseline I need before moving into more advanced modules later this year.

***

## Task 1: Introduction

The terminal might look intimidating at first, but it is actually much faster and more powerful than clicking through a GUI. Many of the most important security tools only run in the terminal, so mastering these basics is a mandatory step.

Question: What does CLI stand for?

> Answer: Command Line Interface

***

## Task 2: Navigation Mission: "Find the Missing Notes"

This task is all about moving around the file system. We used the following commands to explore our surroundings and locate our mission objectives:

```bash
# Check current directory path
pwd

# List files in the current directory
ls

# List files in the current directory in the form of a list
ls -l

# List all files including hidden ones
ls -a

# Change directory into Documents
cd Documents

# Move back up one directory
cd ..

# Search for the mission file
find ~ -name "mission_brief.txt"

# Read the contents of the file
cat /home/ubuntu/Documents/mission_brief.txt
```

Knowing how to efficiently search for files is a core skill for any investigation.

Question: What is the full path of the mission\_brief.txt?

> Answer: /home/ubuntu/Documents/.research/archive/mission\_brief.txt

Question: What is the message inside the mission\_brief.txt?

> Answer: MISSION-FOUND

***

## Task 3: Investigating the System

As a defender, I need to know exactly what kind of environment I am working in. This task covers commands used to gather system metadata. These are often the first commands an analyst (or an attacker) runs to gain situational awareness on a system.

```bash
# Identify the current user
whoami

# Display operating system
uname

# Display system information and kernel version
uname -a

# Check disk space usage in human readable format
df -h

# Check specific OS release information
cat /etc/os-release

# Locate and read the end of day report
find /home/ubuntu -name "day1_report.txt"
cat /home/ubuntu/.logs/archive/day1_report.txt
```

Question: What is the output of the whoami command?

> Answer: ubuntu

Question: What is the kernel version?

> Answer: 6.14.0-1018-aws

Question: How much free disk space is reported?

> Answer: 58G

Question: What is the message in the day\_one\_report.txt?

> Answer: END-OF-DAY1

***

## Conclusion

That wraps up Linux CLI Basics! Navigating the terminal with precision is a core skill that makes the rest of the learning path possible. It might seem simple now, but these building blocks are what allow us to eventually manage complex servers and automate security tasks.
