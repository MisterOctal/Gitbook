---
icon: puzzle-piece
cover: >-
  https://assets.tryhackme.com/additional/banners/Cyber%20Defence%20Frameworks%20icon%20and%20banner-02.svg
coverY: 0
coverHeight: 138
---

# MITRE

**Date:** 13.04.2026

**Room Category:** Walkthrough

**Core Concept:** MITRE provides a suite of globally accessible knowledge bases, including ATT\&CK, D3FEND, and CAR, which standardize the language used to describe adversary tactics and defensive countermeasures.

MITRE is a not-for-profit organization that conducts research to support its mission of "solving problems for a safer world." In cybersecurity, their frameworks have become the industry standard for mapping threat actor behavior. This room explores how to use these tools to profile threats, identify defensive gaps, and implement validated detection analytics.

***

## Task 1: Introduction

MITRE’s cybersecurity frameworks enable both red and blue teams to understand adversary behavior, develop more effective detections, and enhance their defenses.

Question: I understand the learning objectives and am ready to learn about MITRE!

> **Answer:** No answer needed

***

## Task 2: ATT\&CK® Framework

<figure><img src="../.gitbook/assets/image (148).png" alt="" width="375"><figcaption></figcaption></figure>

The MITRE ATT\&CK (Adversarial Tactics, Techniques, and Common Knowledge) framework categorizes the standard TTPs used by attackers:

* **Tactic:** The adversary's goal (the "Why").
* **Technique:** How they achieve the goal (the "How").
* **Procedure:** The specific implementation or execution.

The **ATT\&CK Matrix** provides a visual representation of these tactics. Analysts use the **ATT\&CK Navigator** to annotate and explore these layers.

1. **Tactic**: Let's say that an attacker wants to perform Reconnaissance on their target. This is the attacker's goal.
2. **Technique**: They may utilize the Active Scanning technique. This is how they achieve their Reconnaissance goal.
3. **Sub-technique**: Active Scanning comprises three specific methods: Scanning IP Blocks, Vulnerability Scanning, or Wordlist Scanning.

<figure><img src="../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

Question: What Tactic does the Hide Artifacts technique belong to in the ATT\&CK Matrix?

> **Answer:** Defense Evasion

Question: Which ID is associated with the Create Account technique?

> **Answer:** T1136

***

## Task 3: ATT\&CK in Operation

ATT\&CK provides a consistent language for describing behavior, bridging the gap between raw threat intelligence and defensive operations.

* **CTI Teams:** Map behavior to create actionable profiles.
* **SOC Analysts:** Link activity to tactics to prioritize incidents.
* **Detection Engineers:** Design SIEM/EDR rules based on techniques.

ATT\&CK can be used to map attacks on organizations with known APT groups. For instance, in the image below, an attack has been mapped to the Mustang Panda APT group.

<figure><img src="../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

Question: In which country is Mustang Panda based?

> **Answer:** China

Question: Which ATT\&CK technique ID maps to Mustang Panda’s Reconnaissance tactics?

> **Answer:** T1598

Question: Which software is Mustang Panda known to use for Access Token Manipulation?

> **Answer:** Cobalt Strike

***

## Task 4: ATT\&CK for Threat Intelligence

<figure><img src="../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

Security analysts use ATT\&CK to research specific sectors. For example, in the aviation sector, mapping groups like APT33 helps identify specific risks to infrastructure like Office 365.

Question: Which APT group has targeted the aviation sector and has been active since at least 2013?

> **Answer:** APT33

Question: Which ATT\&CK sub-technique used by this group is a key area of concern for companies using Office 365?

> **Answer:** Cloud Accounts

Question: According to ATT\&CK, what tool is linked to the APT group and the sub-technique you identified?

> **Answer:** Ruler

Question: Which mitigation strategy advises removing inactive or unused accounts to reduce exposure to this sub-technique?

> **Answer:** User Account Management

Question: What Detection Strategy ID would you implement to detect abused or compromised cloud accounts?

> **Answer:** DET0546

***

## Task 5: Cyber Analytics Repository (CAR)

MITRE CAR is a knowledge base of analytics developed to detect behavior defined in ATT\&CK. It provides:

* **Pseudocode:** Human-readable logic.
* **Implementations:** Actual queries for tools like Splunk or EQL.

<figure><img src="../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

* **Unit Tests:** Methods to validate if the detection works.

Question: Which ATT\&CK Tactic is associated with CAR-2019-07-001?

> **Answer:** Defense Evasion

Question: What is the Analytic Type for Access Permission Modification?

> **Answer:** Situational Awareness

***

## Task 6: MITRE D3FEND Framework

<figure><img src="../.gitbook/assets/image (157).png" alt="" width="563"><figcaption></figcaption></figure>

While ATT\&CK focuses on how attacks happen, **D3FEND** (Detection, Denial, and Disruption Framework Empowering Network Defense) focuses on how to stop them. It establishes a common language for defensive techniques like "Credential Rotation" or "User Behavior Analysis."

Question: Which sub-technique of User Behavior Analysis would you use to analyze the geolocation data of user logon attempts?

> **Answer:** User Geolocation Logon Pattern Analysis

Question: Which digital artifact does this sub-technique rely on analyzing?

> **Answer:** Network Traffic

***

## Task 7: Other MITRE Projects

MITRE continues to evolve with new specialized frameworks:

* **Caldera:** An automated adversary emulation tool.

<figure><img src="../.gitbook/assets/image (158).png" alt="" width="160"><figcaption></figcaption></figure>

* **AADAPT:** Focused on digital asset payment technologies (Blockchain/Crypto).
* **ATLAS:** Focused on threats targeting AI and Machine Learning systems.

<figure><img src="../.gitbook/assets/image (159).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What technique ID is associated with Scrape Blockchain Data in the AADAPT framework?

> **Answer:** ADT3025

Question: Which tactic does LLM Prompt Obfuscation belong to in the ATLAS framework?

> **Answer:** Defense Evasion

***

## Conclusion

Mastering MITRE’s resources is essential for any modern security professional. By moving beyond simple Indicators of Compromise (IoCs) like IP addresses and focusing on Tactics, Techniques, and Procedures (TTPs), defenders can build much more resilient security postures that are harder for adversaries to bypass.
