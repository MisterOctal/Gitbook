---
icon: skull
cover: >-
  https://cdn.discordapp.com/attachments/771248078652309515/1493032003145044140/CityScape.jpg?ex=69dd7de8&is=69dc2c68&hm=d1110ef83fd9077f609ae05052d23513adde14d7c8fc9aa4680d4ef873352e6d
coverY: 0
coverHeight: 138
---

# Unified Kill Chain

**Date:** 13.04.2026

**Room Category:** Walkthrough

**Core Concept:** The Unified Kill Chain (UKC) is an 18-phase modern framework that maps the end-to-end lifecycle of a cyber attack.

The UKC is a critical evolution in threat modeling because it moves away from the "linear" thinking of older frameworks, allowing defenders to better anticipate how an attacker pivots through a network and maintains persistence after the initial breach.

***

## Task 1: Introduction

Understanding the behaviors, objectives, and methodologies of a cyber threat is a vital step to establishing a strong cybersecurity posture. The UKC helps defenders understand an attacker's motivation and tactics across the entire attack surface.

Question: Let's proceed with the room!

> **Answer:** No answer needed

***

## Task 2: What is a "Kill Chain"

<figure><img src="../.gitbook/assets/image (169).png" alt="" width="188"><figcaption></figcaption></figure>

Originating from the military, a “Kill Chain” explains the various stages of an attack. In cybersecurity, it describes the path an attacker (like an APT) takes to intrude a target, such as scanning, exploiting a vulnerability, and escalating privileges.

Question: Where does the term "Kill Chain" originate from?

> **Answer:** Military

***

## Task 3: What is "Threat Modelling"

<figure><img src="../.gitbook/assets/image (168).png" alt="" width="119"><figcaption></figcaption></figure>

Threat modeling involves identifying risks by assessing which systems are critical, what vulnerabilities they have, and creating a plan to secure them. The UKC encourages threat modeling by identifying potential attack surfaces.

Question: What is the technical term for a piece of software or hardware in IT (Information Technology?)

> **Answer:** Asset

***

## Task 4: Introducing the Unified Kill Chain

<figure><img src="../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

Published by Paul Pols in 2017, the UKC aims to complement other frameworks. It is more detailed than traditional models, featuring 18 phases that account for the fact that attackers often loop back and forth between stages during a breach.

Question: In what year was the Unified Kill Chain framework released?

> **Answer:** 2017

Question: According to the Unified Kill Chain, how many phases are there to an attack?

> **Answer:** 18

Question: What is the name of the attack phase where an attacker employs techniques to evade detection?

> **Answer:** Defense Evasion

Question: What is the name of the attack phase where an attacker employs techniques to remove data from a network?

> **Answer:** Exfiltration

Question: What is the name of the attack phase where an attacker achieves their objectives?

> **Answer:** Objectives

***

## Task 5: Goal: In (Initial Foothold)

<figure><img src="../.gitbook/assets/image (170).png" alt="" width="188"><figcaption></figcaption></figure>

This goal focuses on gaining access and establishing a presence.

* **Reconnaissance:** Gathering info (Active/Passive).
* **Weaponization:** Setting up attack infrastructure (C2 servers, payloads).
* **Social Engineering:** Manipulating employees (Phishing, impersonation).
* **Exploitation:** Abuse of vulnerabilities for code execution.
* **Persistence:** Maintaining access (Backdoors, services).
* **Defense Evasion:** Bypassing security controls (WAF, AV, IDS).
* **Command & Control:** Establishing communication with the target.
* **Pivoting:** Reaching otherwise inaccessible internal systems.

<figure><img src="../.gitbook/assets/image (166).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is an example of a tactic to gain a foothold using emails?

> **Answer:** Phishing

Question: Impersonating an employee to request a password reset is a form of what?

> **Answer:** Social Engineering

Question: An adversary setting up the Command & Control server is what phase of the Unified Kill Chain?

> **Answer:** Weaponization

***

## Task 6: Goal: Through (Network Propagation)

<figure><img src="../.gitbook/assets/image (171).png" alt="" width="375"><figcaption></figcaption></figure>

Once a foothold is established, the attacker moves through the network to expand their reach.

* **Discovery:** Gathering internal network and system knowledge.
* **Privilege Escalation:** Gaining higher-level permissions (ROOT/SYSTEM).
* **Execution:** Deploying malicious code from the pivot system.
* **Credential Access:** Stealing account names and passwords (Keylogging, dumping).
* **Lateral Movement:** Moving between systems in the network.

<figure><img src="../.gitbook/assets/image (165).png" alt="" width="563"><figcaption></figcaption></figure>

Question: As a SOC analyst, you pick up an alert pointing to failed logins from an administrator account. What phase would an attacker be seeking to achieve?

> **Answer:** Privilege Escalation

Question: Mimikatz was detected attempting to dump user secrets. Which phase does this correspond to?

> **Answer:** Credential Access

***

## Task 7: Goal: Out (Action on Objectives)

<figure><img src="../.gitbook/assets/image (172).png" alt="" width="188"><figcaption></figcaption></figure>

The final goal involves acting on the primary reason for the intrusion.

* **Collection:** Gathering valuable data (Drives, browsers, email).
* **Exfiltration:** Stealing and removing data from the network.
* **Impact:** Manipulating or destroying assets (Ransomware, DoS).
* **Objectives:** Achieving the strategic goal (Financial gain, reputational damage).

<figure><img src="../.gitbook/assets/image (164).png" alt="" width="563"><figcaption></figcaption></figure>

Question: While monitoring the network, you observe a big traffic spike to an unknown IP. What phase describes this?

> **Answer:** Exfiltration

Question: Personally identifiable information (PII) has been released to the public. What part of the CIA triad is affected?

> **Answer:** Confidentiality

***

## Task 8: Practical

<figure><img src="../.gitbook/assets/image (163).png" alt="" width="188"><figcaption></figcaption></figure>

By matching scenarios to the 18 phases of the UKC, we can reconstruct an attack timeline.

Question: What is the flag?

> **Answer:** THM{UKC\_SCENARIO}

***

## Conclusion

<figure><img src="../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

For a SOC analyst, mastering the UKC is about shifting from a reactive mindset to a proactive one. Instead of seeing isolated alerts, you can begin to see the "chain" of events. This allows for better incident response, as you can predict an attacker's next move based on their current phase, ultimately allowing you to disrupt the operation before they reach their "Action on Objectives."
