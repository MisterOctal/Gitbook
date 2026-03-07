---
icon: display
---

# Operating Systems: Introduction

Date: 07.03.2026

Room Category: Theory

Core Concept: Basic operating system components, kernel vs user space, and OS interfaces.

Diving into operating systems! It is easy to take the OS for granted since we use them every day, but understanding exactly how they manage hardware and resources is a massive part of being a SOC analyst. If I want to eventually do digital forensics or malware analysis, I have to know how the OS processes data under the hood.

***

## Task 1: Introduction

This task sets up the scenario. We are basically looking at a gifted computer and trying to figure out what is going on inside of it. It serves as a great bridge from the previous hardware rooms into the actual software that controls that hardware.

Question: I understand the learning objectives and am ready to learn about operating systems!

> Answer: No answer needed

***

## Task 2: Operating System

This is the most critical theory section. It breaks down the system into layers. At the very bottom is the hardware, and right above that is the OS.

<figure><img src="../.gitbook/assets/image (27).png" alt="" width="269"><figcaption></figcaption></figure>

The OS is split into two main spaces:

* **Kernel Space:** The highly privileged core that talks directly to the hardware. If malware gets in here, it is game over.
* **User Space:** The locked-down area where normal applications run. Applications here have to ask the Kernel for permission to do anything with the hardware.

We also covered OS duties, such as Process Management, Memory Management, and OS Security.

Question: Which OS space has unrestricted access to your computer?

> Answer: Kernel Space

Question: Which OS responsibility manages user accounts and authentication?

> Answer: User Management

Instead of throwing us straight into a command line, this task uses the TryHackMe split-screen to give us a familiar Graphical User Interface (GUI) via an Ubuntu VM. We have to poke around the basic system settings to gather some hardware and OS information.

<figure><img src="../.gitbook/assets/image (28).png" alt="" width="459"><figcaption></figcaption></figure>

Question: What version of Ubuntu MATE is running?

> Answer: 1.26.2

Question: Check out the hardware section. How much memory is allocated to your machine?

> Answer: 1.9 GiB

***

## Task 3: OS Interaction and Landscape

This task highlights the two main ways we interact with an operating system. The GUI is what most everyday users are familiar with (windows, icons, and menus). The Command Line Interface (CLI) is text-based. While the GUI is user-friendly, the CLI offers way more precision and speed, which is exactly why security professionals spend most of their time in the terminal.

We also learn that not all operating systems are built the same. A desktop OS focuses on user experience, while a Server OS is "headless" (no GUI) to save resources and maximize stability. We also touched on mobile systems like Android/iOS, and Embedded OS environments found in IoT devices.

Additionally, there's a small practical task to get us navigating around the Ubuntu VM environment, checking directories, and reading basic text files in this task.

<figure><img src="https://cdn.discordapp.com/attachments/1031890307979149332/1479747612704903218/image.png?ex=69ad29db&#x26;is=69abd85b&#x26;hm=dbb116be7b8f26b86d3c6687311684cda60ea865d4838da058002bbe43472c8a" alt="" width="563"><figcaption></figcaption></figure>

Although THM allows us to use the GUI for this task, I decided to use the CLI to solve the tasks as I need to get more familiar with the CLI for my college work and future job. I used `ls`, `cd`, and `cat` in this task to navigate the file directory and read the flag.

Question: How much disk space is available?

> Answer: ext4

Question: After opening the home directory on the desktop, how many user directories exist?

> Answer: 3

Question: Navigate to Alex's home directory and read the note.txt.

> Answer: THM{new\_pc\_for\_free!}

***

## Conclusion

That wraps up the Operating Systems Introduction! Knowing the absolute boundary between Kernel Space and User Space is a concept I will be coming back to constantly when investigating exploits and vulnerabilities. It is great to have this foundational OS layer documented. Time to keep the momentum going!
