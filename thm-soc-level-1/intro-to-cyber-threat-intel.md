---
icon: inbox-full
cover: https://assets.tryhackme.com/additional/banners/Open%20CTI%20banner.png
coverY: 0
coverHeight: 138
---

# Intro to Cyber Threat Intel

**Date:** 09.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Translating the discipline of Cyber Threat Intelligence (CTI) into actionable SOC L1 tasks, including enriching indicators, utilizing CTI frameworks, and triaging alerts effectively.

The modern Security Operations Center (SOC) is inundated with alerts. Cyber Threat Intelligence (CTI) provides the crucial context needed to differentiate benign network noise from genuine threats, allowing analysts to triage alerts from guesswork to calculated action.

***

## Task 1: Introduction

This room introduces the foundations of Cyber Threat Intelligence. The goal is to elevate an analyst's perspective from merely recognizing an IP address to understanding why that IP address is malicious and how to prove it.

**Learning Objectives:**

* Understand what threat intelligence is and its importance to SOC analysts.
* Know the threat intelligence lifecycle and key indicators of compromise.
* Familiarize yourself with intelligence sharing using feeds, platforms, and industry standards.

Question: Ready to get started!

> **Answer:** No answer needed

***

## Task 2: Cyber Threat Intelligence

CTI provides the context necessary to evaluate raw alerts. It seeks to answer who is behind an alert, their past behavior, and how the organization should respond.

<figure><img src="../.gitbook/assets/image (335).png" alt="" width="375"><figcaption></figcaption></figure>

**Data vs. Information vs. Intelligence**

* **Data:** An unprocessed observable (e.g., an IP address like `45.155.205.3:443`).
* **Information:** Data annotated with facts (e.g., IP registered to Hetzner, first seen yesterday).
* **Intelligence:** Analyzed information that answers the "so-what" (e.g., IP belongs to a BumbleBee C2; block immediately).

**Key Indicator Labels:**

* **Indicator of Compromise (IOC):** Evidence of a breach (e.g., C2 address in logs, malicious file hash).
* **Indicator of Attack (IOA):** A malicious action currently underway (e.g., PowerShell launching an unknown service).
* **Tactics, Techniques, and Procedures (TTP):** An adversary's detailed methodologies mapped to frameworks like MITRE ATT\&CK.

**Feeds vs. Platforms**

* **Feed:** A scheduled stream of raw indicators (CSV, JSON, STIX). Over-ingesting feeds without curation causes alert fatigue.
* **Platform:** A structured repository (e.g., MISP, OpenCTI) that stores indicators, tracks enrichment, maps relationships, and acts as the single source of truth.

**Sources of CTI:**

* Internal telemetry (SIEM logs, EDR detections)
* Commercial services (paid sandboxes, vendor feeds)
* Open-Source Intelligence (OSINT) (AbuseIPDB, URLhaus)
* Communities & ISACs (sector-specific sharing groups)

<figure><img src="../.gitbook/assets/image (334).png" alt="" width="375"><figcaption></figcaption></figure>

**Threat Intelligence Classifications:**

1. **Strategic Intel:** High-level trends mapping out business risks (e.g., annual ransomware trends).
2. **Tactical Intel:** Assessments of adversary behavior and TTPs (e.g., advisory notes on new Visual Basic macro abuse).
3. **Operational Intel:** Campaign-specific details regarding attacker motives and targeted assets.
4. **Technical Intel:** Atomic indicators and artifacts (IPs, hashes) directly related to an attack.

Question: What does CTI stand for?

> **Answer:** Cyber Threat Intelligence

Question: IP addresses, Hashes and other threat artefacts would be found under which Threat Intelligence classification?

> **Answer:** Technical Intel

***

## Task 3: CTI Lifecycle

<figure><img src="../.gitbook/assets/image (336).png" alt="" width="563"><figcaption></figcaption></figure>

The CTI Lifecycle transforms raw data into contextualized, actionable insights.

**Traffic Light Protocol (TLP)** Governs how widely intelligence may be shared:

* **TLP: CLEAR:** No restriction; share publicly.
* **TLP: GREEN:** Share within the peer community, but not publicly.
* **TLP: AMBER:** Organization-wide; external sharing strictly on a need-to-know basis.
* **TLP: RED:** Named recipients only; highly restricted.

**The 6-Phase CTI Lifecycle (Scenario Example)**

1. **Direction:** Defining the mission and setting measurable intelligence requirements (e.g., identifying IPs exploiting PostgreSQL and relevant malware hashes).
2. **Collection:** Gathering raw material from commercial feeds, OSINT, internal platforms (MISP), and vendor reports.
3. **Processing:** Normalizing and correlating the gathered data into standardized formats (e.g., STIX) and deduplicating records. Conflicting TLP labels always default to the strictest level.
4. **Analysis:** Evaluating the relevance of the data against internal logs (e.g., checking Splunk to see if a feed IP actually scanned the internal network) and grading indicators by confidence.
5. **Dissemination:** Delivering tailored intelligence outputs to the appropriate stakeholders (e.g., CSVs to the Firewall team, YARA rules to the Endpoint team, executive summaries to Management).
6. **Feedback:** Measuring the success of the workflow (e.g., calculating the drop in median dwell time for brute-force attacks) and refining the process for the next cycle.

Question: At which phase of the CTI lifecycle is data converted into usable formats through sorting, organising, correlation and presentation?

> **Answer:** Processing

Question: During which phase do security analysts get the chance to define the questions to investigate incidents?

> **Answer:** Direction

***

## Task 4: CTI Standards & Frameworks

Standards and frameworks standardize terminology and rationalise the distribution of threat intel.

**MITRE ATT\&CK** A matrix detailing adversary tactics and techniques. It provides standardized labels (e.g., T1059.001 PowerShell) that act as a universal language for describing malicious behavior across tools and teams.

<figure><img src="../.gitbook/assets/image (337).png" alt=""><figcaption></figcaption></figure>

**MITRE D3FEND** Catalogs how defenders respond. If ATT\&CK maps the attack, D3FEND maps the defensive countermeasure (e.g., responding to a DNS tunnel alert with D3-NTDN DNS-request analysis controls).

**Cyber Kill Chain (Lockheed Martin)** Breaks down adversary actions into seven sequential phases:

<figure><img src="../.gitbook/assets/image (338).png" alt="" width="563"><figcaption></figcaption></figure>

1. **Reconnaissance:** Harvesting emails, OSINT, scanning.
2. **Weaponisation:** Engineering malware tailored for the target.
3. **Delivery:** Sending the payload via email, web links, or USB.
4. **Exploitation:** Breaching system vulnerabilities.
5. **Installation:** Establishing persistence via backdoors or RATs.
6. **Command & Control (C2):** Remotely controlling the compromised system.
7. **Actions on Objectives:** Fulfilling the attack goal (data exfiltration, ransomware encryption).

**Vulnerability Tracking**

* **CVE:** Common Vulnerabilities and Exposures (catalogs vulnerabilities).
* **CVSS:** Common Vulnerability Scoring System (severity scale 0-10).
* **NVD:** National Vulnerability Database (links CVEs to CVSS scores and exploits).

**Sharing Standards**

* **STIX:** Structured Threat Information Expression (JSON schema for describing threats).
* **TAXII:** Trusted Automated eXchange of Indicator Information (Secure APIs to exchange threat intelligence). Supports two models: **Collection** (intel hosted by a producer) and **Channel** (intel published to subscribers).

Question: What sharing models are supported by TAXII?

> **Answer:** Collection and Channel

Question: When an adversary has obtained access to a network and is extracting data, what phase of the kill chain are they on?

> **Answer:** Actions on Objectives

***

## Task 5: Practical Analysis

<figure><img src="../.gitbook/assets/image (339).png" alt="" width="563"><figcaption></figcaption></figure>

CTI is often distributed through detailed threat reports published by research teams like Mandiant, Recorded Future, and Palo Alto Unit42. This practical exercise involved analyzing a simulated threat report in a static site lab to build a complete threat profile and extract specific IOCs.

Question: What was the source email address?

> **Answer:** vipivillain@badbank.com

Question: What was the name of the file downloaded?

> **Answer:** flbpfuh.exe

Question: After building the threat profile, what message do you receive?

> **Answer:** THM{NOW\_I\_CAN\_CTI}
