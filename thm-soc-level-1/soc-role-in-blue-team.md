---
icon: shield
---

# SOC Role in Blue Team

**Date:** 06.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the organizational structure of security teams, the distinction between internal SOCs and MSSPs, and the specialized roles within the Blue Team.

While the SOC L1 analyst is the entry point, it exists within a larger ecosystem of defensive security. Understanding where you sit in the hierarchy helps in navigating a career towards specialized fields like Incident Response, Forensics, or Management.

***

## Task 1: Introduction

This room explores the "Blue Team" landscape, focusing on objectives, company placement, and career advancement.

**Objectives:**

* Understand the Blue Team's purpose.
* Explore the security hierarchy (SOC to CISO).
* Map out career paths starting from L1.

Question: Let's find out!

> **Answer:** No answer needed

***

## Task 2: Security Hierarchy

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

Security priorities vary by industry (Privacy for Law, Availability for Manufacturing, Safety for Healthcare). However, most large organizations follow a standard leadership structure:

* **CEO:** Focuses on business objectives.
* **CISO (Chief Information Security Officer):** Makes high-level security decisions and oversees departments.
* **Security Departments:**
  * **Red Team:** Offensive (Ethical Hackers).
  * **GRC Team:** Governance, Risk, and Compliance.
  * **Blue Team:** Defensive (SOC, Engineers, CIRT).

Question: Which senior role typically makes key cyber security decisions?

> **Answer:** CISO

Question: What is the common name for roles like SOC analysts and engineers?

> **Answer:** Blue Team

***

## Task 3: Meet the Blue Team

The Blue Team is dedicated to defensive security, monitoring for attacks and responding to them.

#### Sub-departments:

<figure><img src="../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

1. **SOC (Security Operations Center):** The "First Line of Defense." Includes L1/L2 analysts and engineers who triage alerts and create detection rules.

<figure><img src="../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. **CIRT (Cyber Incident Response Team):** The "Firefighters." Called in for critical breaches. Roles include Forensic Experts and Malware Analysts. Examples include JPCERT and Mandiant.

<figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

3. **Specialized Roles:** \* **Digital Forensics:** Disk/Memory analysis.

* **Threat Intel:** Tracking threat actor groups.
* **AppSec:** Securing the software lifecycle.
* **AI Researcher:** Defending against AI-driven threats.

Question: Does Blue Team focus on defensive or offensive security?

> **Answer:** Defensive

Question: Which department handles active or urgent cyber incidents?

> **Answer:** CIRT

***

## Task 4: Advancing SOC Career

Starting as an L1 analyst provides a broad view of the cyber world. A key distinction in the first job is the environment:

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

<table data-header-hidden><thead><tr><th width="122.5999755859375"></th><th width="256"></th><th></th></tr></thead><tbody><tr><td>Topic</td><td>Internal SOC</td><td>MSSP (Managed Security Service Provider)</td></tr><tr><td><strong>Focus</strong></td><td>One company's systems.</td><td>Multiple diverse customers.</td></tr><tr><td><strong>Pace</strong></td><td>Usually calmer, deep focus.</td><td>High-pressure, high volume.</td></tr><tr><td><strong>Tools</strong></td><td>A few tools, mastered deeply.</td><td>Dozens of different platforms.</td></tr><tr><td><strong>Experience</strong></td><td>Fewer major attacks/year.</td><td>Constant exposure to breaches.</td></tr></tbody></table>

Question: How would you call a cyber security company providing SOC services?

> **Answer:** MSSP

Question: Which role naturally continues your SOC L1 analyst journey?

> **Answer:** SOC L2 Analyst

***

## Task 5: Final Challenge

As a CISO, you must assign the right roles to specific incidents:

* **Incident Response:** Managing a live ransomware outbreak.
* **Digital Forensics:** Investigating a compromised laptop.
* **SOC L1:** Initial triage of a suspicious login alert.

In this task, we interact with a static lab and assign team members to specific issues, such as a PCI DSS audit or an alert triage, solving it earns us our flag.

Question: What flag did you claim after completing the final challenge?

> **Answer:** THM{trysecureme\_is\_secured!}

***

## Conclusion

The SOC L1 role is just the beginning. By gaining experience in triage and understanding how the Blue Team interacts with GRC and the CISO, you can specialize in engineering, response, or leadership.
