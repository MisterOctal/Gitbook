---
icon: gear-code
cover: https://assets.tryhackme.com/room-banners/sigma.png
coverY: 0
coverHeight: 138
---

# Introduction to SOAR

**Date:** 09.04.2026

**Category:** Walkthrough

**Core Concept:** Understanding how Security Orchestration, Automation, and Response (SOAR) helps a SOC team manage the flood of alerts and disconnected tools.

As cyber threats get more complex, SOC teams run into a lot of walls like alert fatigue and manual, slow processes. SOAR acts as a bridge between all your different security tools, letting analysts automate the boring stuff so they can focus on the real threats.

***

## Task 1: Introduction

A modern SOC uses a lot of different solutions like SIEM, EDR, and firewalls. But as the volume of alerts grows, analysts often find themselves overwhelmed. This room explores how SOAR can fix these issues and make a SOC team much more efficient.

Question: Let's get started!

> **Answer:** No answer needed

***

## Task 2: Traditional SOC and Challenges

Before jumping into SOAR, it is important to understand how a traditional SOC works. They handle monitoring, recovery, and threat intelligence, but they often struggle because their tools don't talk to each other.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="262"><figcaption></figcaption></figure>

The biggest hurdles usually include:

* **Alert Fatigue:** Getting hammered by so many alerts that the important ones get missed.
* **Disconnected Tools:** Having to manually jump between a firewall, a SIEM, and an EDR tool just to investigate one event.
* **Manual Processes:** Relying on memory or unwritten rules instead of documented steps, which slows everything down.
* **Talent Shortage:** Not having enough people to keep up with the workload.

Question: How would you describe the experience of an overload of security events being triggered within a SOC?

> **Answer:** Alert Fatigue

***

## Task 3: Overcoming SOC Challenges with SOAR

SOAR is basically the "connective tissue" for a SOC. It brings everything into one interface so you don't have to keep switching tabs.

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

There are three main pillars to how it works:

* **Orchestration:** This is the act of integrating different tools into a single workflow. Instead of manually checking an IP's reputation, the SOAR tool does it for you by talking to your Threat Intel platform.
* **Automation:** This is where the platform follows a "Playbook" to handle tasks without an analyst needing to click anything.
* **Response:** The ability to actually take action, like blocking an IP or disabling a user account, directly from the SOAR interface.

Even with all this power, we still need SOC analysts. SOAR is great for the repetitive tasks, but it can't replace the human judgment needed for a complex investigation.

Question: The act of connecting and integrating security tools and systems into seamless workflows is known as?

> **Answer:** Orchestration

Question: What do we call a predefined list of actions to handle an incident?

> **Answer:** Playbook

***

## Task 4: Building SOAR Playbooks

Playbooks are just step by step instructions for the SOAR tool. We looked at two common examples:

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Phishing Playbook:** This handles the tedious work of checking attachments and URLs in suspicious emails. It can even notify users or delete the email automatically if it's confirmed as malicious.

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **CVE Patching Playbook:** This helps manage new vulnerabilities by checking if your systems are affected and tracking the patching process. If a patch can't be applied, it prompts the team to come up with a different mitigation plan.

Question: Is manual analysis vital within a SOAR workflow? Yay or Nay?

> **Answer:** yay

Question: From where is the CVE Patching playbook fetching the new CVEs?

> **Answer:** Advisory lists

Question: In the CVE Patching playbook, if the assets are found vulnerable even after the patch is deployed, what does the SOC develop next?

> **Answer:** mitigation plan

***

## Task 5: Threat Intel Workflow Practical

For the practical side of this room, we set up a Threat Intelligence integration. This involved mapping out the logic needed for the SOAR platform to automatically process incoming indicators of compromise.

Question: What is the flag received?

> **Answer:** THM{AUT0M@T1N6\_S3CUR1T¥}

***

## Conclusion

SOAR really changes the game for a SOC. By moving away from manual, repetitive work and using automation and orchestration, a team can respond to threats much faster. It turns a reactive "firefighting" environment into a much more organized and proactive operation.
