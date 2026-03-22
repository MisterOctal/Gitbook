---
icon: terminal
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1728058105202.png
coverY: 0
coverHeight: 138
---

# Windows Powershell

**Date:** 20.03.2026

**Room Category:** Walkthrough

**Core Concept:** Transitioning from CMD to the object-oriented power of PowerShell for automation and security analysis.

PowerShell is more than just a shell; it is a full scripting language and configuration management framework built on the .NET framework. Unlike CMD, which processes plain text, PowerShell deals with objects.

***

## Task 1: Introduction

Ahoy there! This is the second room in the Command Line module. It is an introductory room to PowerShell, the second, only historically command-line utility built for the Windows operating system.

<figure><img src="../.gitbook/assets/image (12).png" alt="" width="332"><figcaption></figcaption></figure>

Question: Raise the anchor, hoist the sails—it's time to set sail!

> **Answer:** No answer needed

***

## Task 2: What Is PowerShell

PowerShell is a cross-platform task automation solution made up of a command-line shell, a scripting language, and a configuration management framework. Initially created for Windows, it now supports macOS and Linux.

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What do we call the advanced approach used to develop PowerShell?

> **Answer:** object-oriented

***

## Task 3: PowerShell Basics

PowerShell uses **cmdlets** (command-lets), which follow a consistent **Verb-Noun** naming convention, making commands easier to understand and discover.

Question: How would you retrieve a list of commands that start with the verb Remove?

> **Answer:** Get-Command -Name Remove\*

Question: What cmdlet has its traditional counterpart echo as an alias?

> **Answer:** Write-Output

Question: What is the command to retrieve some example usage for the cmdlet New-LocalUser?

> **Answer:** Get-Help New-LocalUser -examples

## Task 4: Navigating the File System and Working with Files

PowerShell offers cmdlets for file system management. While `dir` and `cd` work as aliases, the native cmdlets provide more structured output.

```
# Display contents of the C:\Users directory
Get-ChildItem -Path C:\Users
```

Question: What cmdlet can you use instead of the traditional Windows command type?

> **Answer:** Get-Content

Question: What PowerShell command would you use to display the content of the "C:\Users" directory?

> **Answer:** Get-ChildItem -Path C:\Users

Question: How many items are displayed by the command described in the previous question?

> **Answer:** 4

***

## Task 5: Piping, Filtering, and Sorting Data

PowerShell uses **piping (|)** to send objects from one cmdlet to another. We use `Where-Object` to filter data based on specific properties.

Question: How would you retrieve the items in the current directory with size greater than 100? \[for the sake of this question, avoid the use of quotes (" or ') in your answer]

> **Answer:** Get-ChildItem | Where-Object -Property Length -gt 100

***

## Task 6: System and Network Information

PowerShell can retrieve detailed system and network information in a structured way, which is vital for enumeration during a security audit.

Question: Other than your current user and the default “Administrator” account, what other user is enabled on the target machine?

> **Answer:** p1r4t3

Question: What is the motto he has so bluntly put as his account’s description?

> **Answer:** A merry life and a short one.

Question: Can you navigate the filesystem and find the hidden treasure inside this pirate’s home?

> **Answer:** THM{p34rlInAsh3ll}

***

## Task 7: Real-Time System Analysis

PowerShell provides advanced tools to analyze running processes, services, and active network connections.

Question: What is the hash of the file that contains the treasure?

> **Answer:** 71FC5EC11C2497A32F8F08E61399687D90ABE6E204D2964DF589543A613F3E08

Question: What property retrieved by default by Get-NetTCPConnection contains information about the process that has started the connection?

> **Answer:** OwningProcess

Question: Can you find the service name that has been tampered with?

> **Answer:** p1r4t3-s-compass

***

## Task 8: Scripting

PowerShell is a powerful scripting language used for automating tasks across multiple systems. One key feature is remote execution using the `Invoke-Command` cmdlet.

Question: What is the syntax to execute the command Get-Service on a remote computer named "RoyalFortune"?

> **Answer:** Invoke-Command -ComputerName RoyalFortune -ScriptBlock {Get-Service}

***

## Conclusion

We've now covered the basics of PowerShell, from the Verb-Noun syntax to piping objects and performing remote execution. These skills are fundamental for any Windows-based security work.
