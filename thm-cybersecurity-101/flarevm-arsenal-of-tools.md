---
icon: toolbox
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1729043928499.png
coverY: 0
coverHeight: 138
---

# FlareVM: Arsenal of Tools

**Date:** 05.04.2026

**Room Category:** Walkthrough

**Core Concept:** Exploring FlareVM, a specialized Windows-based environment packed with utilities for malware analysis, incident response, and reverse engineering.

Setting up a secure and comprehensive malware analysis environment from scratch is incredibly time-consuming. FlareVM (Forensics, Logic Analysis, and Reverse Engineering) solves this problem by providing a heavily customized, pre-configured Windows distribution. Developed by the FLARE Team at FireEye, this toolkit contains dozens of industry-standard applications designed to help analysts dissect malicious executables and safely monitor their behavior.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (5).png" alt="" width="357"><figcaption></figcaption></figure>

FlareVM is a carefully curated collection of specialized tools designed specifically to meet the needs of reverse engineers, incident responders, and forensic investigators. Because it contains live malware samples for practice, this environment operates in strict isolation. Attempting to install all of these tools manually on a standard Windows machine would take several hours and require significant configuration to avoid conflicts.

Question: I'm ready to learn more about FlareVM!

> **Answer:** No answer needed

***

## Task 2: Arsenal of Tools

The FlareVM environment is packed with specialized applications grouped by their primary investigative purpose. Familiarizing yourself with these categories helps narrow down exactly which tool to use during different phases of an incident response engagement.

* **Reverse Engineering & Debugging:** Tools like **Ghidra**, **x64dbg**, and **Radare2** allow analysts to take compiled software apart to understand its core logic at the assembly level.
* **Disassemblers & Decompilers:** Applications like **CFF Explorer** and **RetDec** break machine code into a more understandable, human-readable format.
* **Static & Dynamic Analysis:** Utilities like **Process Hacker** and **PEview** are used to inspect code without executing it (static) or observe its behavior while it runs (dynamic).
* **Forensics & Incident Response:** Frameworks like **Volatility** and **FTK Imager** are utilized for acquiring disk images and performing deep memory forensics.
* **Network Analysis:** **Wireshark** and **Nmap** are essential for capturing network traffic and mapping vulnerabilities.
* **File Analysis:** Hex editors like **HxD** allow for precise manipulation and viewing of raw binary data.
* **Sysinternals Suite:** A vital collection of Microsoft utilities, including **Autoruns**, **Process Explorer**, and **Process Monitor**, used for deep system troubleshooting and tracking malicious persistence mechanisms.

Question: Which tool is an Open-source debugger for binaries in x64 and x32 formats?

> **Answer:** x64dbg

Question: What tool is designed to analyze and edit Portable Executable (PE) files?

> **Answer:** CFF Explorer

Question: Which tool is considered a sophisticated memory editor and process watcher?

> **Answer:** Process Hacker

Question: Which tool is used for Disc image acquisition and analysis for forensic use?

> **Answer:** FTK Imager

Question: What tool can be used to view and edit a binary file?

> **Answer:** HxD

***

## Task 3: Commonly Used Tools for Investigation: Overview

Before diving into complex reverse engineering, most investigations begin with a standard set of triage tools. These utilities provide immediate insight into a file's capabilities and its impact on the host system.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

* **Process Monitor (Procmon):** Records real-time file system, registry, and thread activity. It is highly effective for seeing exactly which files a piece of malware is attempting to read or modify (e.g., unauthorized access to `lsass.exe` for credential dumping).

<figure><img src="../.gitbook/assets/image (7).png" alt="" width="563"><figcaption></figcaption></figure>

* **Process Explorer (Procexp):** Shows the parent-child relationships of active processes. This is vital for determining if a seemingly harmless Word document unexpectedly spawned a malicious command prompt.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

* **HxD:** A hex editor that allows analysts to view the raw byte structure of a file. For example, identifying the `4D 5A` (MZ) header immediately confirms a file is a Windows executable, regardless of its extension.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

* **CFF Explorer:** Used to generate file hashes and inspect the structural headers of Portable Executables, verifying if a system file has been maliciously altered.

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

* **PEStudio:** Performs automated static analysis on an executable to calculate its entropy (which indicates packing or encryption) and lists suspicious API imports.
* **FLOSS:** The FLARE Obfuscated String Solver automatically extracts and de-obfuscates strings from a binary, revealing hidden IP addresses, registry keys, and URLs that standard string extraction tools might miss.

Question: Which tool was formerly known as FireEye Labs Obfuscated String Solver?

> **Answer:** FLOSS

Question: Which tool offers in-depth insights into the active processes running on your computer?

> **Answer:** Process Explorer

Question: By using the Process Explorer (procexp) tool, under what process can we find smss.exe?

> **Answer:** System

Question: Which powerful Windows tool is designed to help you record issues with your system's apps?

> **Answer:** Procmon

Question: Which tool can be used for Static analysis or studying executable file properties without running the files?

> **Answer:** PEStudio

Question: Using the tool PEStudio to open the file cryptominer.bin in the Desktop\Sample folder, what is the sha256 value of the file?

> **Answer:** E9627EBAAC562067759681DCEBA8DDE8D83B1D813AF8181948C549E342F67C0E

Question: Using the tool PEStudio to open the file cryptominer.bin in the Desktop\Sample folder, how many functions does it have?

> **Answer:** 102

Question: What tool can generate file hashes for integrity verification, authenticate the source of system files, and validate their validity?

> **Answer:** CFF Explorer

Question: Using the tool CFF Explorer to open the file possible\_medusa.txt in the Desktop\Sample folder, what is the MD5 of the file?

> **Answer:** 646698572AFBBF24F50EC5681FEB2DB7

Question: Use the CFF Explorer tool to open the file possible\_medusa.txt in the Desktop\Sample folder. Then, go to the DOS Header Section. What is the e\_magic value of the file?

> **Answer:** 5A4D

***

## Task 4: Analyzing Malicious Files!

This practical exercise combines static and dynamic analysis to investigate two suspicious files: `windows.exe` and `cobaltstrike.exe`.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**Static Triage with PEStudio:** Opening `windows.exe` in PEStudio reveals multiple red flags. The file has an extremely high entropy score of 7.999, which strongly indicates the binary is packed or encrypted to evade detection. Furthermore, examining the manifest shows it requires administrative privileges. Looking at its imported functions reveals API calls like `set_UseShellExecute` (used for spawning other processes) and `RijndaelManaged` (an encryption function often used by ransomware to lock files).

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

**Dynamic Observation with Process Monitor and Process Explorer:** When running `cobaltstrike.exe`, dynamic analysis tools allow us to observe its behavior in real time. Using Process Explorer, it is immediately clear that the file was spawned by `explorer.exe`. Checking the TCP/IP properties of the process, and verifying it with Procmon filters, confirms that the executable is actively beaconing out to an external Command and Control (C2) server on port 81.

Question: Using PEStudio, open the file windows.exe. What is the entropy value of the file windows.exe?

> **Answer:** 7.999

Question: Using PEStudio, open the file windows.exe, then go to manifest (administrator section). What is the value under requestedExecutionLevel?

> **Answer:** requireAdministrator

Question: Which function allows the process to use the operating system's shell to execute other processes?

> **Answer:** set\_UseShellExecute

Question: Which API starts with R and indicates that the executable uses cryptographic functions?

> **Answer:** RijndaelManaged

Question: What is the Imphash of cobaltstrike.exe?

> **Answer:** 92EEF189FB188C541CBD83AC8BA4ACF5

Question: What is the defanged IP address to which the process cobaltstrike.exe is connecting?

> **Answer:** 47\[.]120\[.]46\[.]210

Question: What is the destination port number used by cobaltstrike.exe when connecting to its C2 IP Address?

> **Answer:** 81

Question: During our analysis, we found a process called cobaltstrike.exe. What is the parent process of cobaltstrike.exe?

> **Answer:** explorer.exe

***

## Conclusion

FlareVM acts as an unparalleled workstation for reverse engineering and forensics. By integrating tools like PEStudio, FLOSS, Process Monitor, and Process Explorer into a single environment, analysts can seamlessly pivot from static code review to real-time dynamic execution. Mastery of this toolkit significantly decreases the time required to understand and neutralize complex malware threats.
