---
icon: windows
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1750696988683
coverY: 0
coverHeight: 138
---

# Windows Threat Detection 3

**Date:** 02.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Exploring how threat actors maintain long-term access to breached Windows hosts, focusing on the Command and Control, Persistence, and Impact tactics.

After a successful breach and initial reconnaissance, attackers often aim to establish persistent control over the system, turning the compromised host into a reliable foothold for future operations. This document outlines the methodologies for detecting Command and Control (C2) channels, various persistence mechanisms, and the ultimate impact of these breaches.

***

## Task 1: Introduction

The objective is to uncover how a compromised host becomes part of a larger attack. This involves understanding Command and Control (C2) mechanisms, uncovering persistence methods via Windows event logs, and identifying the final impact of a cyberattack.

Question: Let's go!

> **Answer:** No answer needed

***

## Task 2: Command and Control

Threat actors require a method to send commands and maintain control over a victim's host, mapped to the Command and Control (C2) MITRE tactic.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Attacks Without C2** In certain scenarios, such as an RDP breach, a dedicated C2 is unnecessary. Attackers can execute commands directly within the RDP session. However, this access is volatile and is lost as soon as the RDP session is closed or secured.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Implementing C2** For other Initial Access methods (like phishing), an active process must constantly communicate with the attacker's server.

* **Simple C2:** The initial phishing attachment acts directly as the C2 channel.
* **Advanced C2:** The initial attachment downloads a secondary C2 malware, hides it in a directory like `C:\Temp`, and launches it as a stealthy background process. This ensures the connection survives even if the victim deletes the original attachment.

Question: Which suspicious archive did the user download?

> **Answer:** URGENT!.zip

Question: Where did the attackers hide the C2 malware file?

> **Answer:** C:\Users\Administrator\AppData\Roaming\update.exe

Question: What is the domain of the Command and Control server?

> **Answer:** route.m365officesync.workers.dev

***

## Task 3: Persistence Overview

Maintaining reliable, long-term access that survives system reboots and password changes is known as Persistence.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Persisting via RDP and Backdoored Users** While attackers can repeatedly abuse an exposed RDP service, they often deploy alternative persistence methods in case the initial vulnerability is patched. A common approach is creating a new user, elevating it to an administrator, and using it for subsequent RDP logins.

**Command Line User Management:**

* Create a user: `net user "mr.backd00r" "p@ssw0rd!" /add` or `New-LocalUser`
* Add to Administrators: `net localgroup Administrators "mr.backd00r" /add` or `Add-LocalGroupMember`

**Detecting Backdoored Users in Security Logs:**

* **Event ID 4720:** Detects user creation. Investigation should focus on the creator's Logon ID, source IP, and the time of creation.
* **Event ID 4732:** Detects adding a user to a privileged group (typically Administrators or Remote Desktop Users).
* **Event ID 4724:** Detects an attacker resetting the password of an existing, dormant account instead of creating a new one.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: How many times did the threat actor fail to log in to the Administrator?

> **Answer:** 6

Question: After the successful login, which backdoor user did the attacker create?

> **Answer:** support

Question: Which privileged group was the backdoor user added to?

> **Answer:** Administrators

***

## Task 4: Persistence: Tasks and Services

If an attack originates from phishing or a USB infection, remote login via RDP might not be possible. Attackers must ensure their malware runs automatically upon system startup.

**Detecting Malicious Services** Services execute upon OS startup and require administrative privileges to create.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Creation Command:** `sc create "BadService" binpath= "C:\malware.exe" start= auto`
* **Detection:** \* Sysmon Event ID 1 (tracking `sc.exe` execution).
  * Security Event ID 4697 or System Event ID 7045 (Service creation).
  * Suspicious processes spawning with a `services.exe` parent.

**Detecting Scheduled Tasks** Scheduled tasks are highly configurable, making them a prevalent persistence method for Advanced Persistent Threats (APTs).

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Creation Command:** `schtasks /create /tn "BadTask" /tr "C:\malware.exe" /sc onstart /ru System`
* **Detection:**
  * Sysmon Event ID 1 (tracking `schtasks.exe` execution).
  * Security Event ID 4698 (Scheduled task creation).
  * Suspicious processes spawning with a `svchost.exe [...] -s Schedule` parent.

Question: Which Windows service was created to persist the Nessie malware?

> **Answer:** Data Protection Service

Question: Which scheduled task was created to persist the Troy malware?

> **Answer:** AmazonSync

Question: What flag do you get after finding and running the Troy malware?

> **Answer:** THM{c2\_is\_on\_schedule!}

***

## Task 5: Persistence: Run Keys and Startup

For scenarios where administrative privileges are unavailable, or the malware only needs to execute when a specific user logs in, per-user persistence mechanisms are utilized.

**Detecting Startup Folder Persistence** Malware is copied into the user's Startup directory (`%AppData%\Microsoft\Windows\Start Menu\Programs\Startup\`).

<figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Detection:** Monitor file creation events (Sysmon Event ID 11) within the Startup folder. Processes launched via this method will have `explorer.exe` as the parent process.

**Detecting Run Keys Persistence** Malware paths are added to the Windows Registry "Run" keys.

<figure><img src="../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Registry Path:** `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
* **Detection:** Monitor registry modification events (Sysmon Event ID 13) affecting the Run keys.

Question: What is the parent process image of the "Odin" malware?

> **Answer:** C:\Windows\explorer.exe

Question: What is the last line that the "Odin" malware outputs?

> **Answer:** Done doing bad stuff!

Question: What flag do you get after finding and running the "Kitten" malware?

> **Answer:** THM{persisting\_in\_basket!}

***

## Task 6: Impact and Threat Detection Recap

Attackers maintain persistence to execute high-impact actions rather than immediately exiting after data theft.

**Primary Reasons for Persistence:**

* Adding the host to a botnet for further attacks (cryptomining, DDoS).
* Spying on the victim as part of long-term state-sponsored espionage.
* Utilizing the victim as a beachhead to map and breach the wider corporate network.

**Active Directory and Ransomware** In corporate environments, the ultimate goal is often the compromise of the Active Directory infrastructure, culminating in the deployment of ransomware. Ransomware encrypts servers, steals data, and halts operations completely, causing catastrophic business impact. Detecting threats at the Initial Access stage is critical to preventing these disruptive outcomes.

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: What is the biggest threat to most corporate Windows networks?

> **Answer:** Ransomware

Question: At which stage is it best to detect and stop the attack (e.g. Exfiltration)?

> **Answer:** Initial Access

***

## Conclusion

Understanding Command and Control, Persistence, and Impact completes the picture of advanced attack campaigns. Recognizing why attackers establish C2 channels and how they embed themselves using scheduled tasks, services, startup folders, and run keys ensures swift detection and remediation using Windows event logs.
