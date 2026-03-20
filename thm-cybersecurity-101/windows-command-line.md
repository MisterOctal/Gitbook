---
icon: terminal
---

# Windows Command Line

Date: 20.03.2026&#x20;

Room Category: Walkthrough&#x20;

Core Concept: Mastering the Windows Command Prompt (cmd.exe) for speed, efficiency, and remote management.

While graphical interfaces are intuitive, the Command Line Interface (CLI) is a essential tool for security professionals. It uses fewer system resources, allows for rapid automation, and is often the only way to interact with a system during remote management or after gaining initial access via a shell.

***

## Task 1: Introduction

Command Line Interfaces (CLI) can be challenging at first but offer significant advantages in speed and efficiency. For example, finding an IP address is much faster via CLI than clicking through multiple GUI menus. This room focuses on using the default Windows interpreter, cmd.exe, to gather system info, troubleshoot networks, and manage files.

Question: What is the default command line interpreter in the Windows environment?

> Answer: cmd.exe

***

## Task 2: Basic System Information

This task covers how to identify the system you are working on. The 'ver' command gives a quick version string, while 'systeminfo' provides a deep dive into the processor, memory, and hotfix status.

<figure><img src="../.gitbook/assets/image (68).png" alt="" width="188"><figcaption></figcaption></figure>

Question: What is the OS version of the Windows VM?

> Answer: 10.0.20348.2655

Question: What is the hostname of the Windows VM?

> Answer: WINSRV2022-CORE

***

## Task 3: Network Troubleshooting

Networking commands are the bread and butter of connectivity testing. 'ipconfig /all' is used for detailed adapter info, including MAC addresses and gateways. For identifying active connections and listening ports, 'netstat' is the primary tool.

Question: Which command can we use to look up the server’s physical address (MAC address)?

> Answer: ipconfig /all

Question: What is the name of the service listening on port 135?

> Answer: RPCSS

Question: What is the name of the process listening on port 3389?

> Answer: TermService

***

## Task 4: File and Disk Management

Navigating the filesystem involves using 'cd' to move and 'dir' to list contents. To see hidden files, 'dir /a' is required. When interacting with file contents, 'type' displays the text directly in the terminal, while 'copy' and 'move' allow for filesystem manipulation.

Question: What are the file’s contents in C:\Treasure\Hunt?

> Answer: THM{CLI\_POWER}

***

## Task 5: Task and Process Management

Managing running applications is critical for performance and security. 'tasklist' shows everything currently running, which can be narrowed down using filters (/FI). If a process needs to be stopped, 'taskkill' is used with the Process ID (PID).

Question: What command would you use to find the running processes related to notepad.exe?

> Answer: tasklist /FI "imagename eq notepad.exe"

Question: What command can you use to kill the process with PID 1516?

> Answer: taskkill /PID 1516

***

## Task 6: Conclusion

The command line is versatile and supports many more functions than those covered here, such as 'chkdsk' for disk health or 'sfc /scannow' for system file repair. Most commands support the '/?' flag to display their specific help pages.

Below are the two final commands to wrap up the room:

Question: The command shutdown /s can shut down a system. What is the command you can use to restart a system?

> Answer: shutdown /r

Question: What command can you use to abort a scheduled system shutdown?

> Answer: shutdown /a
