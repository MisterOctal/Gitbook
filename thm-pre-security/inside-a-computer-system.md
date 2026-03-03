---
icon: display
---

# Inside a Computer System

Date: 03.03.2026

Room Category: Theory

Core Concept: Understanding different types of computers, their form factors, and uses.

Taking a step back to look at the physical hardware! It's easy to get lost in network protocols and software, but as a future SOC analyst, I have to know what's actually under the hood of the endpoints I'm defending. Understanding how RAM works versus storage, or how a system boots up, is foundational for digital forensics and malware analysis. Plus, this hardware and boot process theory is definitely going to show up on my Security+ exam this November!

***

## Task 1: Introduction

This task gives a brief overview of what we're going to cover: the internal components of a computer and the step-by-step process of what happens when it turns on.

Question: Let's get started!

> Answer: No answer needed

***

## Task 2: Inside a Computer System

This task gives us a quick refresher on the insides of a computer system. We have to deploy a static lab, answer a short McQ quiz about the components of a computer system, and drag and drop some components into the right slot. Doing so gives us a flag.

Question: Give in the flag you received after completing the exercise on the static site.

> THM{4llpccomp0n3nts1d3nt1f13d}

***

## Task 3: What Happens When You Press the Start Button?

This is arguably the most important task for security. When you hit the power button, the system runs a POST (Power-On Self-Test) to ensure hardware is functional. Then, the BIOS or UEFI takes over and looks for a Bootloader (like GRUB for Linux or Windows Boot Manager). The bootloader then loads the Operating System into RAM. Understanding this chain is critical because advanced malware, like bootkits or rootkits, will try to infect the UEFI or bootloader to compromise the system before the OS or antivirus even starts!

<figure><img src="../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure>

Now, we just have to deploy a static lab and answer some McQ and drag and drops again to receive a flag!

Question: What is the flag that you received after completing the exercise?

> Answer: THM{pc5ucce55fully5t4rt3d}

***

## Conclusion

That wraps up "Inside a Computer System"! A short and sweet room, but reviewing the boot sequence and hardware components is a great refresher. It really puts into perspective how physical hardware and software interact. One more room down on the path to finishing my HND by October and crushing that Security+!
