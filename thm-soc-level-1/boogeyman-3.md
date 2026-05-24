---
icon: ghost
cover: >-
  https://cdn.discordapp.com/attachments/1498609087838158952/1506944195112341636/image.png?ex=6a101aa5&is=6a0ec925&hm=a2cecebef920d24d9742863bfdcfd0cd9b4d1f811438de7ba56c392e3307e93a
coverY: 0
coverHeight: 138
---

# Boogeyman 3

**Date:** 21.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Threat hunting, log analysis, lateral movement detection, and credential dumping investigation using ELK Stack.

Analyzing this intrusion requires utilizing an ELK Stack (Elasticsearch, Logstash, Kibana) deployment to query system logs, trace process execution trees, and correlate suspicious activities across multiple compromised endpoints.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (423).png" alt="" width="375"><figcaption></figcaption></figure>

After a period of dormancy following their initial attacks, the Boogeyman threat group returned. This module tasks security personnel with tracking the attacker's footprint through network and endpoint logs. The goal is to piece together the entire attack chain, from the initial phishing email payload to the eventual attempt at deploying ransomware across the domain.

Question: The Boogeyman emerges from the darkness again.

> **Answer:** No answer needed

***

## Task 2: The Chaos Inside

Without tripping the primary security defenses of Quick Logistics LLC, the Boogeyman compromised an employee and waited for the optimal moment to expand the attack. Utilizing this initial access, the threat actors targeted the CEO, Evan Hutchinson, with a questionable email.

Despite the suspicious nature of the email, the CEO opened the attachment. After observing no immediate action, the CEO reported the phishing attempt to the security team.

During the initial investigation, the security team discovered the email attachment in the victim's downloads folder. Furthermore, a file was observed hidden inside the ISO payload. The security team presumed the incident occurred between August 29 and August 30, 2023.

The investigation begins by tracking the initial execution of the payload to determine the exact process that compromised the machine.

Question: What is the PID of the process that executed the initial stage 1 payload?

> **Answer:** 6392

Following the execution of the payload, the script attempted to hide a specific file on the filesystem to serve as a staging point for further malicious actions.

Question: The stage 1 payload attempted to implant a file to another location. What is the full command-line value of this execution?

> **Answer:** "C:\Windows\System32\xcopy.exe" /s /i /e /h D:\review.dat C:\Users\EVAN\~1.HUT\AppData\Local\Temp\review.dat

Once the file was successfully implanted, the initial payload utilized a built-in Windows utility to execute the hidden code.

Question: The implanted file was eventually used and executed by the stage 1 payload. What is the full command-line value of this execution?

> **Answer:** "C:\Windows\System32\rundll32.exe" D:\review.dat,DllRegisterServer

To ensure access survived system reboots, the attacker's script established a persistence mechanism on the CEO's workstation.

Question: The stage 1 payload established a persistence mechanism. What is the name of the scheduled task created by the malicious script?

> **Answer:** Review

The execution of the implanted file subsequently initiated a network connection to an external server, establishing a Command and Control (C2) channel.

Question: The execution of the implanted file inside the machine has initiated a potential C2 connection. What is the IP and port used by this connection? (format: IP:port)

> **Answer:** 165.232.170.151:80

Upon discovering that the compromised account held local administrator privileges, the attacker executed a specific technique to bypass User Account Control (UAC) and operate silently.

Question: The attacker has discovered that the current access is a local administrator. What is the name of the process used by the attacker to execute a UAC bypass?

> **Answer:** fodhelper.exe

Operating with high privileges, the attacker needed a tool to extract credentials from the system's memory. A command was issued to download a well-known credential dumping utility directly from a public repository.

Question: Having a high privilege machine access, the attacker attempted to dump the credentials inside the machine. What is the GitHub link used by the attacker to download a tool for credential dumping?

> **Answer:** https://github.com/gentilkiwi/mimikatz/releases/download/2.2.0-20220919/mimikatz\_trunk.zip

The attacker successfully extracted credentials from the first machine and used them to authenticate to a different account within the network using a pass-the-hash technique.

Question: After successfully dumping the credentials inside the machine, the attacker used the credentials to gain access to another machine. What is the username and hash of the new credential pair? (format: username:hash)

> **Answer:** itadmin:F84769D250EB95EB2D7D8B4A1C5613F2

With access to the new account, the attacker enumerated accessible network shares to find scripts or configurations that could provide further access.

Question: Using the new credentials, the attacker attempted to enumerate accessible file shares. What is the name of the file accessed by the attacker from a remote share?

> **Answer:** IT\_Automation.ps1

Analyzing the contents of the remote file revealed hardcoded credentials, which the attacker immediately leveraged for lateral movement.

Question: After getting the contents of the remote file, the attacker used the new credentials to move laterally. What is the new set of credentials discovered by the attacker? (format: username:password)

> **Answer:** QUICKLOGISTICS\allan.smith:Tr!ckyP@ssw0rd987

The attacker utilized these new credentials to target a specific workstation within the environment.

Question: What is the hostname of the attacker's target machine for its lateral movement attempt?

> **Answer:** WKSTN-1327

To confirm the successful lateral movement, it is necessary to identify the parent process of the commands executed on the newly compromised machine.

Question: Using the malicious command executed by the attacker from the first machine to move laterally, what is the parent process name of the malicious command executed on the second compromised machine?

> **Answer:** wsmprovhost.exe

Once established on the second machine, the attacker repeated the process of dumping credentials from memory to find higher-privileged accounts.

Question: The attacker then dumped the hashes in this second machine. What is the username and hash of the newly dumped credentials? (format: username:hash)

> **Answer:** administrator:00f80f2538dcb54e7adc715c0e7091ec

Securing domain administrator credentials allowed the attacker to target the Domain Controller directly. The attacker utilized a DCSync attack to replicate directory data and steal additional sensitive accounts.

Question: After gaining access to the domain controller, the attacker attempted to dump the hashes via a DCSync attack. Aside from the administrator account, what account did the attacker dump?

> **Answer:** backupda

With total control over the domain, the attacker initiated the final phase of the operation by downloading a malicious binary designed to encrypt the network.

Question: After dumping the hashes, the attacker attempted to download another remote file to execute ransomware. What is the link used by the attacker to download the ransomware binary?

> **Answer:** http://ff.sillytechninja.io/ransomboogey.exe
