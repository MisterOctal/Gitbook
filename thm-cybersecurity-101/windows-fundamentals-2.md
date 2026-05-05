---
icon: windows
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/61add44b3c6be8004802cc37/room-content/4094ed0a54f8dc274b9b4f602c57b152.svg
coverY: 0
coverHeight: 139
---

# Windows Fundamentals 2

**Date:** 18.03.2026

**Room Category:** Walkthrough

**Core Concept:** Exploring advanced Windows administrative tools, system configuration, and the Windows Registry.

Building on the first module, Windows Fundamentals 2 moves from the basic GUI and file system into the actual management of the OS. For a security professional, knowing how to manipulate services, audit the registry for persistence, and manage system resources is critical. These tools are often the first place we look when investigating a suspicious process or a system that isn't behaving as expected.

***

## Task 1: Introduction

This room is a deep dive into the internal management utilities of Windows. While the average user might only open Task Manager to kill a frozen app, an analyst uses these tools to see exactly what is running, what starts automatically, and how the system's "brain", the Registry is configured.

Question: Read above and start the virtual machine.

> **Answer:** No answer needed

***

## Task 2: System Configuration (MSConfig)

The System Configuration utility (`msconfig`) is primarily used to troubleshoot the Windows boot process. It allows you to toggle "Safe Boot" and manage which services and startup items are allowed to run.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="436"><figcaption></figcaption></figure>

From a defensive standpoint, checking the "Services" tab (and hiding Microsoft services) is a quick way to find third-party software that might be malicious or unnecessary.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="437"><figcaption></figcaption></figure>

Additionally, the "Tools" tab provides a centralized list of administrative commands, which is useful for identifying the underlying executables for various Windows utilities.

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="440"><figcaption></figcaption></figure>

While MSConfig handles boot-level configurations, **Advanced System Settings** (via `sysdm.cpl`) allows for deeper OS-level tuning. Key areas include:

* **Environment Variables:** Defining system-wide paths like `%windir%` or `%TEMP%`.
* **Performance Options:** Managing virtual memory (paging file) and visual effects.
* **User Profiles:** Managing local and roaming user data.
* **Startup and Recovery:** Configuring system failure behavior and dump file generation for debugging.

Question: What is the name of the service that lists Systems Internals as the manufacturer?

> **Answer:** PsShutdown

Question: Whom is the Windows license registered to?

> **Answer:** Windows User

Question: What is the command for Windows Troubleshooting?

> **Answer:** C:\Windows\System32\control.exe /name Microsoft.Troubleshooting

Question: What command will open the Control Panel?

> **Answer:** control.exe

***

## Task 3: Change UAC Settings

As we learned in Part 1, User Account Control (UAC) is the gatekeeper for administrative actions. In this task, we look at the different levels of UAC notification. While you can technically turn it off using `UserAccountControlSettings.exe`, doing so on a production machine is a massive security risk, as it allows any process to gain administrative rights without user interaction.

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="528"><figcaption></figcaption></figure>

Question: What is the command to open User Account Control Settings?

> **Answer:** UserAccountControlSettings.exe

***

## Task 4: Computer Management

Computer Management (`compmgmt.msc`) is a "snap-in" tool that combines several utilities into one interface. It includes the Task Scheduler, Event Viewer, and Shared Folders.

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="279"><figcaption></figcaption></figure>

For a SOC analyst, the Event Viewer is the most important part of this tool, as it records every significant action taken on the system.

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="367"><figcaption></figcaption></figure>

The Task Scheduler is also a critical area to audit, as attackers frequently use it to maintain persistence by scheduling malicious scripts to run at startup or on a specific trigger.

Question: What is the command to open Computer Management?

> **Answer:** compmgmt.msc

Question: When is the npcapwatchdog scheduled task set to run at?

> **Answer:** At system startup

Question: What is the name of the hidden folder that is shared?

> **Answer:** sh4r3dF0Ld3r

***

## Task 5: System Information

The System Information tool (`msinfo32.exe`) provides a comprehensive summary of the hardware, system components, and software environment. It is an excellent tool for quick enumeration to see the exact OS version, BIOS mode, and total physical memory. Analysts often check the Environment Variables section here to see paths like `%SystemRoot%`, which points to where the OS is installed.

Question: What is the command to open System Information?

> **Answer:** msinfo32.exe

Question: What is listed under System Name?

> **Answer:** THM-WINFUN2

Question: Under Environment Variables, what is the value for ComSpec?

> **Answer:** %SystemRoot%\system32\cmd.exe

***

## Task 6: Resource Monitor

Resource Monitor (`resmon.exe`) provides a much more granular view of system performance than the basic Task Manager. It allows you to see exactly which files a process is reading/writing and which network IP addresses a process is communicating with. In a malware analysis scenario, this is vital for seeing if a suspicious file is communicating with an external Command and Control (C2) server.

<figure><img src="../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the command to open Resource Monitor?

> **Answer:** resmon.exe

***

## Task 7: Command Prompt

While PowerShell is the modern standard, the classic Command Prompt (`cmd.exe`) is still heavily used for basic networking and system tasks. Commands like `ipconfig /all` provide detailed information about network adapters, including physical (MAC) addresses and DHCP lease information.

Question: In System Configuration, what is the full command for Internet Protocol Configuration?

> **Answer:** C:\Windows\System32\cmd.exe /k %windir%\system32\ipconfig.exe

Question: For the ipconfig command, how do you show detailed information?

> **Answer:** ipconfig /all

***

## Task 8: Registry Editor

The Registry (`regedt32.exe`) is the central hierarchical database used to store software and hardware configurations. It is divided into "Hives" (like `HKEY_LOCAL_MACHINE` and `HKEY_CURRENT_USER`). Attackers love the Registry because it offers numerous locations to hide "Run" keys, which ensure their malware starts every time the computer boots up.

<figure><img src="../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="380"><figcaption></figcaption></figure>

Question: What is the command to open the Registry Editor?

> **Answer:** regedt32.exe

***

## Conclusion

This room highlighted the power of the built-in Windows administrative suite. Moving beyond the standard user interface allows us to see the "gears" of the operating system. Mastering the Registry Editor and Resource Monitor specifically provides a significant advantage when performing incident response or system hardening.
