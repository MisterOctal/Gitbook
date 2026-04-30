---
icon: windows
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/61add44b3c6be8004802cc37/room-content/4094ed0a54f8dc274b9b4f602c57b152.svg
coverY: 0
coverHeight: 139
---

# Windows Fundamentals 1

**Date:** 18.03.2026

**Room Category:** Walkthrough

**Core Concept:** An introduction to the Windows operating system, its GUI, file system, and basic administrative tools.

Moving from Linux over to Windows fundamentals is essential. Even though I prefer the Linux terminal for most heavy lifting, a huge portion of the corporate world runs on Windows. To be a well-rounded security professional, I need to know exactly how Windows handles users, permissions, and system files. This knowledge is the bedrock for later learning about Active Directory and Windows-specific attacks.

***

## Task 1: Windows Editions

Windows is a family of operating systems tailored for different needs. While Home is for general consumers, Pro includes advanced features like Domain Join and specialized encryption. Enterprise provides the most robust management and security suite for large organizations, including features like AppLocker and DirectAccess.

Question: What encryption can you enable on Pro that you can't enable in Home?

> **Answer:** BitLocker

***

## Task 2: The Desktop (GUI)

The Graphical User Interface (GUI) allows for intuitive interaction with the OS. Beyond the Start Menu, the Taskbar and Notification Area (System Tray) are critical for monitoring system status and background applications. In a security context, the Taskbar can be customized to hide elements to prevent user tampering or to clean up an analyst's workstation.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

The above screenshot is an example of a typical Windows Desktop and below are the core components of the GUI:

1. The Desktop
2. Start Menu
3. Search Box (Cortana)
4. Task View
5. Taskbar
6. Toolbars
7. Notification Area

Question: Which selection will hide/disable the Search box?

> **Answer:** Hidden

Question: Which selection will hide/disable the Task View button?

> **Answer:** Show Task View Button

Question: Besides Clock and Network, what other icon is visible in the Notification Area?

> **Answer:** Action Center

***

## Task 3: Introduction to Windows

Windows has a long history dating back to 1985. It has evolved from a simple GUI shell for MS-DOS into a sophisticated multi-user operating system. Versions like Windows XP and Windows 7 were highly successful, while Vista and 8 were often criticized for performance and interface changes. Today, Windows 11 is the standard for end-users, while Windows Server 2022/2025 powers corporate backends.

Question: Read above and start the virtual machine.

> **Answer:** No answer needed

***

## Task 4: The File System

Windows primarily uses the NTFS (New Technology File System) format. Unlike Linux's single-root tree (/), Windows uses drive letters (like C:) to represent different partitions or physical disks. NTFS is superior to older systems like FAT32 because it supports ACLs (Access Control Lists), file compression, and journaling, which helps prevent data corruption.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="542"><figcaption></figcaption></figure>

What is the meaning of NTFS?

> **Answer:** New Technology File System

***

## Task 5: The Windows\System32 Folders

The System32 directory (C:\Windows\System32) is the heart of the Windows OS. It houses the kernel (ntoskrnl.exe), hardware drivers (.sys files), and core system utilities. Because this folder is so sensitive, Windows uses environment variables to help programs find it regardless of which drive the OS is installed on. The most common variable for the Windows directory is %windir%.

Question: What is the system variable for the Windows folder?

> **Answer:** %windir%

***

## Task 6: User Accounts, Profiles, and Permissions

User management is central to Windows security. Every user has a unique profile directory (C:\Users\<username>) and belongs to specific groups that define their level of access.

Question: What is the name of the other user account?

> **Answer:** tryhackmebilly

Question: What groups is this user a member of?

> **Answer:** Remote Desktop Users,Users

Question: What built-in account is for guest access to the computer?

> **Answer:** Guest

Question: What is the account description?

> **Answer:** window$Fun1!

***

## Task 7: User Account Control

UAC is a security boundary that helps mitigate the impact of malware. It ensures that even administrative users operate with standard privileges by default. When an application attempts a "privileged" action (like installing software or editing registry keys), UAC prompts the user for confirmation or administrative credentials.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="332"><figcaption></figcaption></figure>

Question: What does UAC mean?

> **Answer:** User Account Control

***

## Task 8: Settings and the Control Panel

Microsoft is currently transitioning all system management to the modern Settings app, but the legacy Control Panel remains essential for deep administrative tasks. In the Control Panel, switching the view to "Small Icons" reveals a more granular list of utilities, including administrative tools and specialized drivers.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="544"><figcaption></figcaption></figure>

Question: In the Control Panel, change the view to Small icons. What is the last setting in the Control Panel view?

> **Answer:** Windows Defender Firewall

***

## Task 9: Task Manager

Task Manager (Ctrl+Shift+Esc) is the go-to tool for real-time system monitoring. It provides visibility into processes, performance, startup applications, and active users. Checking the Startup tab is a primary location to check for malware persistence.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="496"><figcaption></figcaption></figure>

Question: What is the keyboard shortcut to open Task Manager?

> **Answer:** Ctrl+Shift+Esc

***

## Conclusion

This room provided a solid walk through the fundamental building blocks of the Windows OS. From the specific layout of the System32 folder to the way UAC protects the kernel from unauthorized changes, these concepts are vital. Understanding the Windows environment is just as important as mastering Linux if I want to be effective in a modern SOC or as a penetration tester.
