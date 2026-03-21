---
icon: windows
---

# Windows CLI Basics

**Date:** 08.03.2026

**Room Category:** Walkthrough

**Core Concept:** Navigating the Windows file system and gathering system info via the Command Prompt (CMD).

Since I am moving toward a SOC analyst role, getting comfortable with the Windows Command Line is just as important as the Linux terminal. Most corporate environments run on Windows, so knowing how to hunt for files or check system configurations via the CLI is a mandatory skill. This room builds on what we learned in the Linux CLI module but adapts it for the Windows environment.

***

## Task 1: Introduction

The Windows Command Prompt (CMD) is the classic text based interface for interacting with the OS. While PowerShell is more advanced, CMD is still widely used for basic system administration and quick troubleshooting. Mastering these commands allows for faster navigation and better automation compared to the standard GUI.

Question: Read the introduction.

> **Answer:** No answer needed

***

## Task 2: Windows CLI: Navigate, File and Finding the File

This task covers moving through directories and searching for specific files. Unlike Linux, Windows uses backslashes for paths and different command flags. We practiced listing files, including hidden ones, and used the search function to find a specific task brief.

```bash
# Check current directory
cd

# List files in the current directory
dir

# List all files including hidden ones
dir /a

# Change directory to Documents
cd Documents

# Move back to the parent directory
cd ..

# Search for a specific file in all subdirectories
dir /s task_brief.txt

# Read the contents of a file
type "C:\Users\Administrator\Documents\Notes\research_yn6\exports_imv\screenshots\notes_wi6\task_brief.txt"
```

Question: What is the full path of the task\_brief.txt?

> **Answer:** C:\Users\Administrator\Documents\task\_brief.txt

Question: What is the message inside the task\_brief.txt?

> **Answer:** TASK-BRIEF-FOUND

***

## Task 3: Gathering System Information

Defenders use the CLI to quickly audit a system's status. This task introduces commands to identify the user, the computer name, the OS version, and the network configuration. These are the "bread and butter" commands for initial situational awareness.

```bash
# Identify the current user and host
whoami

# Display the computer name
hostname

# Get detailed system information (OS version, RAM, etc.)
systeminfo

# Check network configuration and IP address
ipconfig
```

Question: What is the computer host name?

> **Answer:** thmlab

Question: What is the full Windows version listed in the system info?

> **Answer:** 10.0.17763 N/A Build 17763

***

## Conclusion

That wraps up Windows CLI Basics! It is a great feeling to be able to navigate both Linux and Windows environments through the terminal. These skills are exactly what I will need when I start analyzing actual attack patterns or performing remote investigations. One step closer to finishing the HND and being ready for the more advanced modules.
