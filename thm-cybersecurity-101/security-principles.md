---
icon: shield
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/701fe785951a8e91523d337ba815120e.png
coverY: 0
coverHeight: 138
---

# Security Principles

**Date:** 05.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the CIA triad, security models (Bell-LaPadula, Biba), and architectural design principles like Defense-in-Depth and Zero Trust.

Security is not a binary state but a continuous process of improving a system's posture against specific adversaries. Whether protecting a personal laptop from a toddler or safeguarding million-dollar industrial designs from state-sponsored actors, applying the right security principles is essential for risk management.

***

## Task 1: Introduction

Perfect security is impossible to achieve. Instead, security professionals aim to increase the "cost" of an attack, making it difficult enough that the adversary gives up or is detected. To do this effectively, we must first understand the assets we are protecting and the threats we face.

<figure><img src="../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Think how you would describe something as secure.

> **Answer:** No answer needed

***

## Task 2: CIA (The Security Triad)

The foundation of information security is built upon three pillars known as the **CIA Triad**:

<figure><img src="../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Confidentiality:** Ensuring data is accessible only to authorized users. (e.g., encrypting credit card details).
* **Integrity:** Ensuring data remains accurate and hasn't been tampered with. (e.g., preventing a hacker from changing a shipping address).
* **Availability:** Ensuring systems and data are accessible when needed. (e.g., a web store staying online during a sale).

#### Beyond CIA: Parkerian Hexad

Donn Parker proposed expanding the triad into six elements:

1. **Confidentiality**
2. **Integrity**
3. **Availability**
4. **Authenticity:** Verifying the source of data is genuine.
5. **Possession:** Protecting against the physical loss of control over data/media.
6. **Utility:** Ensuring data remains in a useful format (e.g., not losing a decryption key).

Question: What is the flag that you obtained at the end of the site exercise?

> **Answer:** THM{CIA\_TRIAD}

***

## Task 3: DAD (The Opposite of CIA)

To understand attacks, we look at the inverse of the CIA triad, known as the **DAD Triad**:

<figure><img src="../.gitbook/assets/image (18) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Disclosure:** The opposite of Confidentiality (e.g., a data leak).
* **Alteration:** The opposite of Integrity (e.g., unauthorized modification).
* **Destruction/Denial:** The opposite of Availability (e.g., a DDoS attack or hardware destruction).

Question: The attacker managed to gain access to customer records and dumped them online. What is this attack?

> **Answer:** Disclosure

Question: A group of attackers were able to locate both the main and the backup power supply systems and switch them off. What is this attack?

> **Answer:** Destruction/Denial

***

## Task 4: Security Models

Security models provide a formal framework for implementing security functions:

* **Bell-LaPadula:** Focuses on **Confidentiality**.
  * _Simple Security:_ No Read Up.
  * _Star Property:_ No Write Down.
* **Biba Model:** Focuses on **Integrity**.
  * _Simple Integrity:_ No Read Down.
  * _Star Integrity:_ No Write Up.
* **Clark-Wilson Model:** Focuses on **Integrity** through "Transformation Procedures" and "Integrity Verification."

Question: What is the flag that you obtained at the end of the site exercise?

> **Answer:** THM{SECURITY\_MODELS}

***

## Task 5: Defense-in-Depth

Also known as **Multi-Level Security**, this principle argues that security should be implemented in layers. If one control fails (e.g., a firewall is bypassed), another control (e.g., endpoint antivirus or file encryption) should still protect the asset.

<figure><img src="../.gitbook/assets/image (19) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Make sure you have read the above.

> **Answer:** No answer needed

***

## Task 6: ISO/IEC 19249

ISO/IEC 19249:2017 provides a catalogue of architectural and design principles:

#### Architectural Principles

1. **Domain Separation:** Grouping related components into entities with specific privileges (e.g., Ring 0 vs Ring 3).
2. **Layering:** Imposing policies at different abstract levels (e.g., the OSI model).
3. **Encapsulation:** Hiding implementation details (e.g., OOP or APIs).
4. **Redundancy:** Ensuring availability through backups or parity (e.g., RAID 5).
5. **Virtualization:** Sharing hardware while maintaining isolation.

#### Design Principles

1. **Least Privilege:** Granting only the minimum permissions necessary for a task.
2. **Attack Surface Minimization:** Disabling unnecessary services to reduce entry points.
3. **Centralized Parameter Validation:** Validating all input in a single, trusted library.
4. **Centralized General Security Services:** Using a central server for functions like authentication.
5. **Preparing for Error Handling:** Ensuring systems "fail safe" and don't leak info in error messages.

Question: Which principle are you applying when you turn off an insecure server that is not critical to the business?

> **Answer:** 2

Question: Your company hired a new sales representative. Which principle are they applying when they tell you to give them access only to the company products and prices?

> **Answer:** 1

Question: While reading the code of an ATM, you noticed a huge chunk of code to handle unexpected situations such as network disconnection and power failure. Which principle are they applying?

> **Answer:** 5

***

## Task 7: Zero Trust vs. Trust but Verify

* **Trust but Verify:** Legacy approach where users inside a network are trusted, but their actions are logged and audited.
* **Zero Trust:** Modern approach where trust is considered a vulnerability. "Never trust, always verify." Every request is authenticated and authorized regardless of its origin (internal or external).

Question: Make sure you have read the above.

> **Answer:** No answer needed

***

## Task 8: Threat versus Risk

It is critical to distinguish between these three terms:

<figure><img src="../.gitbook/assets/image (20) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Vulnerability:** A weakness in a system (e.g., a glass window).
* **Threat:** A potential danger that could exploit a weakness (e.g., a thief with a rock).
* **Risk:** The likelihood of a threat exploiting a vulnerability and the resulting impact (e.g., how likely is the window to be broken, and what is the cost of the stolen items?).

Question: Make sure you have read the above.

> **Answer:** No answer needed

***

## Conclusion

Mastering security principles is the first step in moving from a reactive "fix-it" mindset to a proactive architectural mindset. By balancing the CIA triad and applying frameworks like ISO/IEC 19249, we can build resilient systems that protect against both simple accidents and sophisticated adversaries.
