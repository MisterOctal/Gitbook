---
icon: windows
---

# Windows Basics

Date: 07.03.2026

Room Category: Theory

Core Concept: Navigating the Windows interface, File Explorer, and essential system tools.

It is time to look at the most common operating system in the corporate world. While Linux is more prevelant in security systems, as a future SOC analyst, most of the endpoints I will be defending or investigating are going to be running Windows. Getting a solid grasp on how it manages files, handles permissions, and monitors processes is a mandatory skill.&#x20;

***

## Task 1: Introduction

This task gives a high level overview of the Windows operating system. Almost everyone is familiar with the Windows interface from school or home, but this room is about looking at it through a more technical lens. We are taking on the role of a new employee getting familiar with their workstation.

Question: I understand the learning objectives and am ready to learn about Windows!

> Answer: No answer needed

***

## Task 2: Exploring the Windows Workspace

Windows has come a long way since the early versions built on top of MS DOS. We looked at the core components of the modern desktop: the Start Menu, Taskbar, and the Search function. In a security context, the Taskbar and System Tray are important because they show what applications are currently active and communicating over the network.

We are also given a windows 10 VM in this task and need to navigate around it to find the answers for the questions. After opening About your PC, examining the device specifications and windows specifications give us the answer to the first three questions.

<figure><img src="../.gitbook/assets/image.png" alt="" width="421"><figcaption></figcaption></figure>

Question: What is the device name specified in About your PC?

> Answer: TryHatMe

Question: How much RAM is installed in your new PC?

> Answer: 4.00 GB

Question: Which version of Windows Server 2019 Data Center is installed?

> Answer: 1809

Next, we have to navigate some folders, like in the previous room, I chose to the use the CLI instead of the GUI for this step. Upon navigating to the directory and reading the welcome file, we receive the flag needed for the last question in this task!

<figure><img src="../.gitbook/assets/image (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Explore the `TryHatMe Onboarding` folder located on your computer's Desktop.

What is the flag value found within `Welcome.txt`?

> Answer: THM{welcome\_to\_tryhatme!}

***

## Task 3: Configuring and Securing Windows

This task covered the basics of installing and updating software. We looked at the Windows Update service and the different ways applications are installed (via the Microsoft Store or .exe and .msi installers). From a defense perspective, keeping the OS and applications updated is the easiest way to prevent many common exploits.

<figure><img src="../.gitbook/assets/image (2).png" alt="" width="555"><figcaption></figcaption></figure>

We looked at the two main ways to manage system preferences: the modern Windows Settings app and the classic Control Panel. While Microsoft is slowly moving everything to the Settings app, the Control Panel still holds many of the advanced administrative tools that a security professional might need.

<figure><img src="../.gitbook/assets/image (3).png" alt="" width="306"><figcaption></figcaption></figure>

Task Manager is a vital tool for any analyst. It lets you see exactly what processes are running, how much CPU and RAM they are consuming, and which services are active. If a machine is acting weird or might be infected with malware, Task Manager is often the first place I will look to see what is running in the background.

Task Manager has five tabs to help you keep track of your system.

1. Processes: Currently running apps and background processes, and their resource usage
2. Performance: Graphs and statistics for system resources such as CPU, memory, and network
3. Users: Currently logged-in users and used resources&#x20;
4. Details: A more technical view of running processes, including process IDs (PIDs)
5. Services: Windows services and their current status (running or stopped)

<figure><img src="../.gitbook/assets/image (4).png" alt="" width="390"><figcaption></figcaption></figure>

Finally, we looked at the built in security tools like Windows Security (formerly Defender) and the Windows Firewall. We practiced running a custom scan on a specific folder to see how it detects potential threats. Understanding how the native firewall handles inbound and outbound rules is a core part of endpoint protection.

Question: Use the `TryHatMeWelcome` installer located within the `TryHatMe Onboarding` folder.\
What is the flag value you receive after installing and running the application?

> Answer: THM{your\_first\_day!}

<figure><img src="../.gitbook/assets/image (5).png" alt="" width="290"><figcaption></figcaption></figure>

Question: Investigate the Time & Language section of the Windows Settings app.\
Which country or region is your computer currently set to?

> Answer: United States

<figure><img src="../.gitbook/assets/image (7).png" alt="" width="442"><figcaption></figcaption></figure>

Question: Open the Task Manager on your workstation's Desktop and navigate to the Users tab.\
Which account is currently logged in?

> Answer: Administrator

<figure><img src="../.gitbook/assets/image (10).png" alt="" width="440"><figcaption></figcaption></figure>



Question: After performing your custom scan, click `Virus:DOS/EICAR_Test_File` and select See details.\
What is the file name shown in the Affected items section?

> Answer: tryhatmemaldoc.txt

<figure><img src="../.gitbook/assets/image (11).png" alt="" width="491"><figcaption></figcaption></figure>

***

## Conclusion

That wraps up Windows Basics! It was a great refresher on the most common OS environment I will be working in. Having a firm handle on where everything is located and how to use tools like Task Manager makes the more advanced forensics and incident response rooms much less intimidating.
