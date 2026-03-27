---
icon: swords
cover: https://assets.tryhackme.com/room-banners/metasploit.png
coverY: 0
coverHeight: 139
---

# Metasploit: Meterpreter

**Date:** 27.03.2026

**Room Category:** Walkthrough / Exploitation Basics

**Core Concept:** Taking a deep dive into Meterpreter, exploring how in-memory payloads are used for post-exploitation to evade detection and maintain control.

Meterpreter is more than just a shell; it is an advanced, multi-faceted payload that operates as an in-memory agent. It provides a robust command-and-control (C2) interface with the target, allowing for extensive post-exploitation without writing a single file to the disk.

***

## Task 1: Introduction to Meterpreter

Meterpreter runs on the target system as an agent within a C2 architecture. Its primary advantage is its stealthy nature and its ability to interact directly with the operating system API.

#### How it works:

* **In-Memory Execution:** Meterpreter resides entirely in RAM. It does not write an `.exe` or `.dll` to the target's disk, which helps it evade traditional Antivirus (AV) signature-based scans that focus on new files.
* **Process Concealment:** It runs as a process (e.g., hiding inside `spoolsv.exe`). When listing DLLs used by a process, Meterpreter is designed to be indistinguishable from legitimate system activity.
* **Encrypted Communication:** It establishes an encrypted (TLS) channel with the Metasploit server. Unless the network decrypts and inspects HTTPS traffic, IDS/IPS solutions will fail to detect its activities.

**Key Commands for Identification:**

* `getpid`: Returns the Process ID (PID) Meterpreter is currently running under.
* `ps`: Lists all running processes to see where you are hiding.

Question: No answer needed.

> **Answer:** No answer needed

***

## Task 2: Meterpreter Flavors

Meterpreter comes in many "flavors" depending on the target OS and the environment available. Payloads are divided into **staged** (sent in two steps: stager then stage) and **inline** (single step).

#### Supported Platforms:

* **Mobile:** Android, Apple iOS.
* **Desktop:** Windows, Linux, OSX.
* **Web/Scripting:** PHP, Python, Java.

#### Decision Factors:

1. **Target OS:** Use the version specific to the hardware/software (e.g., `linux/x64/meterpreter_reverse_tcp`).
2. **Available Components:** If a target has Python installed, a Python-based payload might be more stable.
3. **Network Constraints:** If raw TCP is blocked, use `reverse_http` or `reverse_https` to tunnel traffic through standard web ports.

Question: No answer needed.

> **Answer:** No answer needed

***

## Task 3: Meterpreter Commands

Typing `help` at the `meterpreter >` prompt displays all available commands. Commands are built-in tools that run on the target without loading additional executables. Note that different flavors (Windows vs. Linux) will show different available categories.

#### Core Commands

Used for session navigation and fundamental interaction.

* **background / bg:** Backgrounds the current session to return to `msfconsole`.
* **exit / terminate:** Terminate the current session.
* **guid:** Get the session Globally Unique Identifier.
* **help / ?:** Displays the command menu.
* **info:** Displays info about a Post module.
* **load:** Loads one or more Meterpreter extensions (e.g., `load kiwi`).
* **migrate:** Moves Meterpreter to another running process for stability.
* **sessions:** Quickly switch to another active session.

#### File System Commands

Direct interaction with the target's files and folders.

* **cd / pwd:** Change or print the current working directory.
* **ls / dir:** List files in the current directory.
* **cat / edit:** View or modify file contents.
* **mkdir / rm / rmdir:** Create or delete files and directories.
* **search:** Locate specific files (e.g., `search -f *.txt`).
* **upload / download:** Move files between the attacker and victim.

#### Networking Commands

Mapping the local network environment.

* **arp:** Displays the host Address Resolution Protocol cache.
* **ifconfig / ipconfig:** Displays network interfaces.
* **netstat:** Shows active network connections.
* **portfwd:** Forwards a local port to a remote service.
* **route:** View and modify the target's routing table.

#### System Commands

Controlling the operating system state.

* **clearev:** Clears the Windows Event Logs (Stealth).
* **execute:** Executes a command on the target.
* **getpid / getuid:** Shows the current PID and user identity.
* **ps / kill:** List or terminate running processes.
* **shell:** Drops into a native system command shell (cmd.exe or /bin/sh).
* **sysinfo:** Returns OS, Computer name, and Architecture info.

#### Interface & Media Commands

* **keyscan\_start / stop / dump:** Captures and retrieves keystrokes.
* **screenshot / screenshare:** Capture an image or real-time view of the desktop.
* **webcam\_list / snap / stream:** Interact with the target's camera.
* **record\_mic:** Records audio from the default microphone.

#### Privilege & Credentials

* **getsystem:** Attempts to automatically elevate privileges to `SYSTEM`.
* **hashdump:** Dumps the contents of the SAM database (Windows hashes).

Question: No answer needed.

> **Answer:** No answer needed

***

## Task 4: Post-Exploitation with Meterpreter

The post-exploitation phase is where Meterpreter truly shines, providing tools to gather intelligence and move laterally.

#### 1. Information Gathering

* **getuid:** Check your current privilege level (e.g., `NT AUTHORITY\SYSTEM`).
* **hashdump:** Lists the content of the SAM database (Windows user hashes).
* **search:** Locate sensitive files using `-f` (e.g., `search -f flag.txt`).

#### 2. Process Migration

Migrating to a different process helps stabilize your connection. If the process you exploited crashes or is closed by a user, you lose your session unless you migrate to a more permanent system process.

* **Command:** `migrate [PID]`

#### 3. Keylogging

By migrating to a process handling user input (like a word processor or browser), you can capture every keystroke.

* **Commands:** `keyscan_start`, `keyscan_dump`, `keyscan_stop`.

#### 4. Interactive Shell

If you need to run standard OS commands not built into Meterpreter, you can drop into a native command shell.

* **Command:** `shell` (Use `CTRL+Z` to return to Meterpreter).

Question: No answer needed.

> **Answer:** No answer needed

***

## Task 5: Post-Exploitation Challenge

This task involves a hands-on simulation using the credentials `ballen : Password1` over SMB to perform post-exploitation on the target host.

Question: What is the computer name?

> **Answer:** ACME-TEST

Question: What is the target domain?

> **Answer:** FLASH

Question: What is the name of the share likely created by the user?

> **Answer:** speedster

Question: What is the NTLM hash of the jchambers user?

> **Answer:** 69596c7aa1e8daee17f8e78870e25a5c

Question: What is the cleartext password of the jchambers user?

> **Answer:** Trustno1

Question: Where is the "secrets.txt" file located? (Full path of the file)

> **Answer:** c:\Program Files (x86)\Windows Multimedia Platform\secrets.txt

Question: What is the Twitter password revealed in the "secrets.txt" file?

> **Answer:** KDSvbsw3849!

Question: Where is the "realsecret.txt" file located? (Full path of the file)

> **Answer:** c:\inetpub\wwwroot\realsecret.txt

Question: What is the real secret?

> **Answer:** The Flash is the fastest man alive

***

## Conclusion

Meterpreter is a premier post-exploitation tool because of its stealth (memory-only execution), its versatility (wide range of flavors), and its powerful command set. By mastering process migration and encrypted communication, a pentester can maintain a persistent, undetected presence on a target network.
