---
icon: windows
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/61add44b3c6be8004802cc37/room-content/4094ed0a54f8dc274b9b4f602c57b152.svg
coverY: 0
coverHeight: 140
---

# Windows Fundamentals 3

**Date:** 18.03.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Understanding Windows security features, including the built-in Firewall, Windows Defender, and Event Viewer auditing.

This final module in the Windows Fundamentals series shifts from management to defense. Knowing how to configure the Windows Firewall and audit system events is a direct prerequisite for the SOC environment I am building in my homelab.

***

## Task 1: Introduction

While the previous rooms focused on how the system works and how to manage it, this room focuses on how the system protects itself. We will explore the built-in security suite that comes standard with modern Windows installations, which is often the first line of defense against network-based attacks and malware.

<figure><img src="../.gitbook/assets/image (15) (1).png" alt="" width="288"><figcaption></figcaption></figure>

Question: Read the above and start the virtual machine.

> **Answer:** No answer needed

***

## Task 2: Windows Updates

Keeping a system patched is the most effective way to prevent exploitation of known vulnerabilities (CVEs). Windows Update manages the delivery of security patches, driver updates, and feature improvements. In an enterprise environment, these updates are often managed centrally to ensure stability before being pushed to all workstations.

Question: There were two definition updates installed in the attached VM. On what date were these updates installed?

> **Answer:** 5/3/2021

***

## Task 3: Windows Security

Windows Security is the central hub for all protection features. It provides an at-a-glance view of the system's "health," including virus protection, firewall status, and app control. For an analyst, this dashboard is a quick way to verify if security features have been tampered with or disabled by an attacker.

<figure><img src="../.gitbook/assets/image (16) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Checking the Security section on your VM, which area needs immediate attention?

> **Answer:** Virus & threat protection

***

## Task 4: Virus & Threat Protection

Windows Defender is a robust, built-in antivirus and anti-malware solution. It uses signature-based detection (matching files against known malware) and heuristic analysis (monitoring for suspicious behavior). In this task, we see how Defender can quarantine or remove threats automatically.

<figure><img src="../.gitbook/assets/image (17) (1).png" alt="" width="339"><figcaption></figcaption></figure>

Question: Specifically, what is turned off that Windows is notifying you to turn on?

> **Answer:** Real-time protection

***

## Task 5: Firewall & Network Protection

The Windows Defender Firewall is a host-based firewall that controls inbound and outbound network traffic based on sets of rules. It is essential for blocking unauthorized access to services like SMB or RDP. Understanding the difference between Domain, Private, and Public network profiles is key to applying the correct level of restriction.

<figure><img src="../.gitbook/assets/image (18) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: If you were connected to airport Wi-Fi, what most likely will be the active firewall profile?

> **Answer:** Public network

***

## Task 6: App & Browser Control

This feature adds layers of protection against malicious websites and potentially unwanted applications (PUAs). It includes "SmartScreen," which checks downloads and web pages against a list of known-safe and known-malicious sites to prevent phishing and drive-by downloads.

<figure><img src="../.gitbook/assets/image (19) (1).png" alt="" width="400"><figcaption></figcaption></figure>

Question: Read the above.

> **Answer:** Warn

***

## Task 7: Device Security

Device Security focuses on hardware-level protection. Features like Core Isolation and Memory Integrity use virtualization-based security to protect sensitive parts of the Windows kernel from being tampered with by high-level malware or rootkits.

<figure><img src="../.gitbook/assets/image (20) (1).png" alt="" width="395"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (21) (1).png" alt="" width="313"><figcaption></figcaption></figure>

Question: What is the TPM?

> **Answer:** Trusted Platform Module

***

## Task 8: BitLocker

As mentioned in the first room, BitLocker is a full-disk encryption feature available on Pro and Enterprise editions. It protects data by encrypting the entire drive, ensuring that if a laptop is stolen, the data cannot be read without the recovery key or the user's credentials.

Question: What must a user insert on computers that DO NOT have a TPM version 1.2 or later?

> **Answer:** Startup Key

***

## Task 9: Volume Shadow Copy Service

The Volume Shadow Copy Service (VSS) allows Windows to take "snapshots" of files or volumes even while they are in use. This is the technology behind "System Restore" and "Previous Versions." Ransomware often tries to delete these shadow copies to prevent users from recovering their files without paying the ransom.

<figure><img src="../.gitbook/assets/image (24) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is VSS?

> **Answer:** Volume Shadow Copy Service

***

## Conclusion

This concludes the Windows Fundamentals module. We now have a comprehensive understanding of the GUI, administrative tools, and the defensive suite built into Windows. These skills are fundamental for your journey toward becoming a security professional and will serve as the backbone for your upcoming "Tug of War" lab phases.
