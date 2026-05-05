---
icon: link-horizontal
cover: >-
  https://assets.tryhackme.com/additional/banners/cyber-defence-frameworks-banner.svg
coverY: 0
coverHeight: 138
---

# Cyber Kill Chain

**Date:** 13.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the Lockheed Martin Cyber Kill Chain, a fundamental framework that outlines the chronological phases an adversary must go through to conduct a successful cyber attack.

By understanding how an attacker thinks and operates step-by-step, defenders can identify intrusions early and "break the chain" before the adversary reaches their ultimate objective.

***

## Task 1: Introduction

<figure><img src="https://tryhackme-images.s3.amazonaws.com/room-icons/66704dd0e54a1f39bff7b1a1-1735573085552" alt="" width="188"><figcaption></figcaption></figure>

The Cyber Kill Chain is based on a military concept and was adapted for cybersecurity in 2011. It's a foundational model for SOC Analysts, Threat Hunters, and Incident Responders. By mapping out an intrusion attempt against this framework, we can identify what stage an attacker is currently in and anticipate their next move.

Question: Read the above.

> **Answer:** No answer needed

***

## Task 2: Reconnaissance

<figure><img src="../.gitbook/assets/image (27) (1).png" alt="" width="188"><figcaption></figcaption></figure>

Before launching an attack, the adversary gathers intelligence on the target. This phase determines the success rate of the entire operation. Reconnaissance can be **Passive** (OSINT, WHOIS lookups, scraping social media) where the attacker doesn't interact with the company, or **Active** (port scanning, probing services) which generates noise on the target's network.

A common technique here is _Email Harvesting_ to prepare for a phishing campaign, using tools like theHarvester or Hunter.io.

Question: What is the name of the Intel Gathering Tool that is a web-based interface to the common tools and resources for open-source intelligence?

> **Answer:** OSINT Framework

Question: What is the definition for the email gathering process during the stage of reconnaissance?

> **Answer:** Email harvesting

***

## Task 3: Weaponization

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

Once the attacker has enough information, they package their exploit and malware into a deliverable payload. They might buy a payload off the Dark Web or craft a custom one to bypass specific AV signatures. A classic example is embedding malicious VBA scripts (macros) into a legitimate-looking Microsoft Office document tailored to the target.

Question: What is the term for automated scripts embedded in Microsoft Office documents that can be used to perform tasks or exploited by attackers for malicious purposes?

> **Answer:** macros

***

## Task 4: Delivery

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

This is how the weaponized payload is transmitted to the target environment. The attacker has to pick the best medium based on their reconnaissance. Common methods include targeted spear-phishing emails, dropping infected USB drives in the company parking lot, or compromising a website the target employees frequently visit.

Question: What do you call an attack targeting a specific group by infecting their frequently visited website?

> **Answer:** Watering hole attack

***

## Task 5: Exploitation

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

This is the exact moment the malicious code is executed on the target's machine, taking advantage of a vulnerability. This could be a user double-clicking that malicious macro, or the attacker exploiting an unpatched public-facing web server or a Zero-day vulnerability.

In a SOC, signs of exploitation usually look like unexpected process spawns or suspicious command-line arguments.

Question: What is the term for a cyber attack that exploits a software vulnerability that is unknown by software vendors?

> **Answer:** Zero-day exploit

***

## Task 6: Installation

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

If an attacker gets disconnected or a vulnerability is patched, they don't want to lose access. In the Installation phase, they establish a persistent backdoor.

They might install a web shell on a compromised web server, create a reverse shell payload using Meterpreter, modify Windows services, or add their malware to the Windows Registry "run keys" so it executes every time the machine boots. They also use techniques like _Timestomping_ to alter file creation dates, making their malware look like a normal system file.

Question: What technique is used to modify file time attributes to hide new or changes to existing files?

> **Answer:** Timestomping

Question: What malicious script can be planted by an attacker on the web server to maintain access to the compromised system and enables the web server to be accessed remotely?

> **Answer:** web shell

***

## Task 7: Command & Control

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

Now the attacker needs to communicate with the compromised system to send instructions. They establish a Command and Control (C2) channel. Because legacy methods like IRC are easily blocked by modern firewalls, attackers usually blend their traffic into normal web browsing by using HTTP/HTTPS, or they tunnel their commands through standard DNS queries (DNS Tunneling).

Question: What is the C2 communication where the victim makes regular DNS requests to a DNS server and domain which belong to an attacker.

> **Answer:** DNS Tunneling

***

## Task 8: Actions on Objectives (Exfiltration)

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

The final phase. The attacker has persistent, remote control over the target and acts on their primary goal. This usually involves lateral movement, privilege escalation, stealing and exfiltrating sensitive data, or deploying ransomware. A key tactic here is deleting Windows Shadow Copies so the victim cannot easily restore their encrypted or corrupted files.

Question: What technology is included in Microsoft Windows that can create backup copies or snapshots of files or volumes on the computer, even when they are in use?

> **Answer:** Shadow Copy

***

## Task 9: Practice Analysis

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

This task involved mapping the notorious 2013 Target data breach to the Cyber Kill Chain using a static site lab. It was a great exercise in taking a real-world news story and breaking it down into operational phases.

Question: What is the flag after you complete the static site?

> **Answer:** THM{7HR347\_1N73L\_12\_4w35om3}

***

## Conclusion

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

While the Cyber Kill Chain is a legendary model, it does have its limits, specifically, it focuses heavily on perimeter defense and malware delivery, which means it struggles to account for Insider Threats or complex cloud-native attacks. Moving forward, it makes sense to combine this foundational knowledge with more modern, granular frameworks like MITRE ATT\&CK.
