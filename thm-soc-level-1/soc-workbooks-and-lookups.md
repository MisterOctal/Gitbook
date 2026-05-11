---
icon: book
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1743519631241
coverY: 0
coverHeight: 138
---

# SOC Workbooks and Lookups

**Date:** 07.04.2026

**Room Category:** Walkthrough

**Core Concept:** Streamlining alert triage using corporate resources like identity inventories, asset lookups, network diagrams, and standardized investigation workbooks (playbooks).

In a SOC, context is everything. An alert is just a signal; workbooks and lookups provide the "who, what, and where" needed to determine if that signal represents a legitimate threat or normal business operations.

***

## Task 1: Introduction

This room explores how SOC analysts use internal corporate resources to simplify complex investigations and ensure consistency across the team.

**Learning Objectives:**

* Understanding SOC investigation workbooks.
* Utilizing asset and identity inventories.
* Interpreting corporate network diagrams.
* Practicing workflow building for various attack scenarios.

Question: I am ready to start!

> **Answer:** No answer needed

***

## Task 2: Assets & Identities

To triage an alert, you must understand the entities involved. This is done through two primary inventories:

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

1. **Identity Inventory:** A catalogue of user accounts, service accounts, roles, and permissions. (Sources: Active Directory, Okta, HR systems like BambooHR).

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

2. **Asset Inventory:** A list of computing resources, including servers and workstations. (Sources: SIEM, EDR, MDM solutions like Intune).

**Scenario Context:** If `G.Baker` (CFO) logs into `HQ-FINFS-02` (Finance File Server) and shares a file with `R.Lund` (US Financial Adviser), the inventories help confirm this is legitimate business behavior.

Question: Looking at the identity inventory, what is the role of R.Lund at the company?

> **Answer:** US Financial Adviser

Question: Checking the asset inventory, what data does the HQ-FINFS-02 server store?

> **Answer:** File server for financial records

Question: Finally, does the file sharing from the scenario look legitimate and expected? (Yea/Nay)

> **Answer:** Yea

***

## Task 3: Network Diagrams

A network diagram is a visual schema of subnets, locations, and connections. It helps analysts reconstruct attack paths, such as lateral movement or unauthorized external access.

<figure><img src="../.gitbook/assets/image (18) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Scenario:** An external IP connects to port `10443`, is translated to an internal IP, and begins scanning internal subnets.

* **10.10.0.53** = VPN Subnet.
* **172.16.15.0/24** = Database Subnet.
* **172.16.23.0/24** = Office Subnet.

Question: According to the network diagram, which service is exposed on the TCP/10443 port?

> **Answer:** vpn

Question: Now, which subnet would the server behind 172.16.15.99 IP belong to?

> **Answer:** Database Subnet

Question: Finally, does the scenario look like a True Positive (TP) or False Positive (FP)?

> **Answer:** TP

***

## Task 4: Workbooks Theory

A **SOC Workbook** (also known as a playbook or runbook) defines the exact steps to investigate and remediate a specific threat. It ensures that L1 analysts don't miss vital details.

<figure><img src="../.gitbook/assets/image (17) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Common Workbook Structure:**

1. **Enrichment:** Gathering context (Identity/Asset lookups, Threat Intel).
2. **Investigation:** Analyzing SIEM logs to verify the activity's legitimacy.
3. **Escalation/Final Actions:** Closing the alert or passing it to L2 for remediation.

Question: Which SOC role would use workbooks the most (e.g. SOC Manager)?

> **Answer:** SOC L1 Analyst

Question: What is the process of gathering user, host, or IP context using TI and lookups?

> **Answer:** Enrichment

Question: Looking at the workbook example, what platform is used as an identity inventory source?

> **Answer:** BambooHR

***

## Task 5: Workbooks Practice

In this practical section, you build modular blocks for different investigation types.

Question: What flag did you receive after completing the first workbook?

> **Answer:** THM{the\_most\_common\_soc\_workbook}

Question: What flag did you receive after completing the second workbook?

> **Answer:** THM{be\_vigilant\_with\_powershell}

Question: What flag did you receive after completing the third workbook?

> **Answer:** THM{asset\_inventory\_is\_essential}

***

## Conclusion

Workbooks and lookups are the backbone of a mature SOC. They reduce the cognitive load on analysts and ensure that every alert is handled with the same level of professional rigor.
