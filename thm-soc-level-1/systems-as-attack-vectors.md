---
icon: swords
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1759767777043
coverY: 0
coverHeight: 138
---

# Systems as Attack Vectors

**Date:** 06.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding how attackers exploit technical flaws (vulnerabilities) and human errors (misconfigurations) to compromise servers, cloud platforms, and end-user devices.

While social engineering targets the user, system-based attacks target the software and hardware directly. A single vulnerable mail server can expose thousands of mailboxes, making systems high-value targets for threat actors.

***

## Task 1: Introduction

This room shifts focus from the human element to the technical systems. We will explore how SOC analysts protect physical servers, virtual machines, and cloud environments.

**Learning Objectives:**

* The role of systems in the digital infrastructure.
* Real-world attack examples (Human-led, Vulnerabilities, Supply Chain).
* Practical remediation and investigation scenarios.

Question: I'm ready to learn!

> **Answer:** No answer needed

***

## Task 2: Definition of System

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/678ecc92c80aa206339f0f23/room-content/678ecc92c80aa206339f0f23-1752596680315.svg" alt="" width="188"><figcaption></figcaption></figure>

A system is any environment where data is processed or stored, this includes laptops, mail servers, or cloud platforms like Microsoft 365. Unlike phishing, these attacks can often occur without any victim intervention if a system is exposed to the internet with a flaw.

| Breached System   | Attack Value                                        |
| ----------------- | --------------------------------------------------- |
| Personal Laptop   | PC becomes part of a botnet; Steam profile theft.   |
| Mail Server       | Mass data dump and blackmail.                       |
| Industrial Server | Ransomware encryption of an entire factory network. |
| Government Panel  | Website defacement or activism.                     |

Question: Can cyber attacks happen without victim intervention (Yea/Nay)?

> **Answer:** Yea

Question: Can a breach of just a single system lead to disastrous consequences (Yea/Nay)?

> **Answer:** Yea

***

## Task 3: Attacks on Systems

Attackers use various entry points to gain a foothold in a network:

1. **Human-Led Attacks:** Users inadvertently start the attack (e.g., using a "RubberDucky" USB or weak passwords).

<figure><img src="../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

2. **Vulnerabilities:** Exploiting flaws in software code. Over 40,000 vulnerabilities were published in 2024 alone.

<figure><img src="../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

3. **Supply Chain Attacks:** Compromising a trusted third-party app or library (like SolarWinds) to gain access to all its users.

Question: What is the term for a security flaw that can be exploited to breach a system?

> **Answer:** Vulnerability

Question: What is the name of the attack when malware comes from a trusted app or library?

> **Answer:** Supply Chain

***

## Task 4: Vulnerabilities

Vulnerabilities are often categorized by their **CVE (Common Vulnerabilities and Exposures)** number.

<figure><img src="../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

* **Zero-Day:** A vulnerability discovered by attackers before the vendor is aware or has a patch ready.
* **Response:** The primary defense against a vulnerability is a **Patch**. If a patch isn't available, SOC analysts must use temporary mitigations like IP restrictions or WAF (Web Application Firewall) rules.

Question: What is the CVE for the critical SharePoint vulnerability dubbed "ToolShell"?

> **Answer:** CVE-2025-53770

Question: How would you respond to a detected vulnerability on your system?

> **Answer:** Patch

***

## Task 5: Misconfigurations

A misconfiguration is a human error in setup, not a coding bug. Examples include leaving default passwords (like "admin/123456") or exposing private databases to the public internet.

<figure><img src="../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

#### Proactive Responses:

* **Penetration Testing:** Authorized simulations of attacks to find flaws.
* **Vulnerability Scans:** Automated tools to find outdated software or weak settings.
* **Configuration Audits:** Manual reviews against industry standards (e.g., CIS Benchmarks).

Question: Can a system patch or software update fix the misconfigurations (Yea/Nay)?

> **Answer:** Nay

Question: Which activity involves an authorized cyber attack to detect the misconfigurations?

> **Answer:** Penetration Testing

***

## Task 6: Practice

Protecting a company requires balancing the defense of both humans and systems. While you can't "train" a server, you can train the IT department to configure it securely and maintain a strict patch management policy.

<figure><img src="../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

**Mitigation Strategies:**

* **Patch Management:** Tracking and updating vulnerable systems.
* **Network Protection:** Restricting access to trusted IP ranges.
* **Antivirus:** Detecting malicious code execution.

Question: What flag did you receive after completing the "Systems at Risk" challenge?

> **Answer:** THM{patch\_or\_reconfigure?}

Question: What flag did you receive after completing the "Remediation Plan" challenge?

> **Answer:** THM{best\_systems\_defender!}

***

## Conclusion

SOC analysts bridge the gap between security and IT. By understanding how systems are attacked, we can better advise the teams responsible for managing them.
