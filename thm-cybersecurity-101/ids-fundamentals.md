---
icon: eye
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1723464092304.png
coverY: 0
coverHeight: 139
---

# IDS Fundamentals

**Date:** 02.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding Intrusion Detection Systems (IDS), their deployment modes, and gaining practical experience with Snort rules and PCAP analysis.

While firewalls act as gatekeepers at the network boundary, an Intrusion Detection System (IDS) acts as a surveillance system inside the network. It monitors traffic that has already bypassed the firewall to identify malicious activities based on known patterns (signatures) or behavioral deviations (anomalies). This room explores the various types of IDS and provides hands-on training with Snort, the industry's most popular open-source IDS.

***

## Task 1: What Is an IDS

An IDS sits within the network to monitor traffic and generate alerts for security administrators when suspicious activity is detected. Unlike an IPS (Intrusion Prevention System), a standard IDS does not block traffic; it merely notifies the team.

* **Firewall:** Gatekeeper (Prevention).
* **IDS:** Surveillance Camera (Detection).

Question: Can an intrusion detection system (IDS) prevent the threat after it detects it? Yea/Nay

> **Answer:** Nay

***

## Task 2: Types of IDS

IDS solutions are categorized based on where they are deployed and how they detect threats.

#### Deployment Modes

* **Host Intrusion Detection System (HIDS):** Installed on individual devices (hosts). Provides deep visibility into local system files and processes but is resource-intensive to manage at scale.
* **Network Intrusion Detection System (NIDS):** Monitors traffic across the entire network. It provides a centralized view of all network-wide threats.

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

#### Detection Modes

* **Signature-Based:** Matches traffic against a database of known attack patterns. It is very fast and accurate for known threats but cannot detect "zero-day" attacks.
* **Anomaly-Based:** Learns a "baseline" of normal network behavior. It flags any deviation from this baseline. While it can detect zero-day attacks, it is prone to false positives.
* **Hybrid:** Combines both signature and anomaly-based techniques to leverage the strengths of both.

Question: Which type of IDS is deployed to detect threats throughout the network?

> **Answer:** Network Intrusion Detection System

Question: Which IDS leverages both signature-based and anomaly-based detection techniques?

> **Answer:** Hybrid IDS

***

## Task 3: IDS Example: Snort

Snort is a powerful, open-source NIDS capable of performing real-time traffic analysis and packet logging. It operates in three primary modes:

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

<table data-header-hidden><thead><tr><th width="148.20001220703125"></th><th width="298.2000732421875"></th><th></th></tr></thead><tbody><tr><td>Mode</td><td>Description</td><td>Use Case</td></tr><tr><td><strong>Packet Sniffer</strong></td><td>Reads and displays packets on the console.</td><td>Troubleshooting network flow.</td></tr><tr><td><strong>Packet Logging</strong></td><td>Logs packets to a disk (often in PCAP format).</td><td>Forensic investigation and root cause analysis.</td></tr><tr><td><strong>NIDS Mode</strong></td><td>Analyzes traffic against rule files to generate alerts.</td><td>Proactive threat monitoring.</td></tr></tbody></table>

Question: Which mode of Snort helps us to log the network traffic in a PCAP file?

> **Answer:** Packet Logging Mode

Question: What is the primary mode of Snort called?

> **Answer:** Network Intrusion Detection System Mode

***

## Task 4: Snort Usage

Snort rules follow a specific syntax to define what traffic triggers an alert. Rules are typically stored in `/etc/snort/rules/local.rules`.

#### Rule Format Structure

A standard Snort rule looks like this: `alert icmp any any -> $HOME_NET any (msg:"Ping Detected"; sid:10001; rev:1;)`

* **Action:** `alert` (tells Snort what to do).
* **Protocol:** `icmp` (the type of traffic).
* **Source/Destination:** IP addresses and ports (or `any`).
* **Metadata:** Contained in parentheses:
  * `msg`: The text shown in the alert.
  * `sid`: Unique Signature ID.
  * `rev`: Revision number for version tracking.

<figure><img src="../.gitbook/assets/image (12) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Where is the main directory of Snort that stores its files?

> **Answer:** /etc/snort

Question: Which field in the Snort rule indicates the revision number of the rule?

> **Answer:** rev

Question: Which protocol is defined in the sample rule created in the task?

> **Answer:** icmp

Question: What is the file name that contains custom rules for Snort?

> **Answer:** local.rules

***

## Task 5: Practical Lab (PCAP Analysis)

In this scenario, Snort is used to analyze a historical traffic capture file (`Intro_to_IDS.pcap`) to identify signs of an intrusion. By running Snort with the `-r` flag, we can process the file against our defined rules.

<figure><img src="../.gitbook/assets/image (13) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

**Analysis Command:** `sudo snort -q -l /var/log/snort -r Intro_to_IDS.pcap -A console -c /etc/snort/snort.conf`

Question: What is the IP address of the machine that tried to connect to the subject machine using SSH?

> **Answer:** 10.11.90.211

Question: What other rule message besides the SSH message is detected in the PCAP file?

> **Answer:** Ping Detected

Question: What is the sid of the rule that detects SSH?

> **Answer:** 1000002

***

## Conclusion

Understanding the difference between HIDS and NIDS, as well as signature vs. anomaly detection, is fundamental for any security analyst. Snort provides a versatile platform to apply these concepts, allowing for both real-time monitoring and historical forensic analysis of network traffic.
