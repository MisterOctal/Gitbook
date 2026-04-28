---
icon: radar
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/61306d87a330ed00419e22e7-1760495811117
coverY: 0
coverHeight: 138
---

# Network Discovery Detection

**Date:** 22.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the mechanics of asset discovery, differentiating between internal and external scanning, and detecting horizontal vs vertical port scans.

Before an attacker can exploit a network, they must map the attack surface. Network discovery is the process of identifying IP addresses, open ports, operating systems, and running services. While defenders also perform discovery to maintain asset inventories and identify unpatched vulnerabilities, SOC analysts must be able to differentiate between authorized maintenance and malicious reconnaissance.

***

## Task 1: Introduction

Discovery is often one of the first visible steps of an attack. This room explores why attackers perform discovery and how to detect various scanning techniques within a network environment.

Question: Let's begin!

> **Answer:** No answer needed

***

## Task 2: Network Discovery

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Attackers scan to find "openings" like vulnerable service versions, while defenders scan to reduce the attack surface. To manage the noise, SOC teams often allowlist known internal scanners and use Threat Intelligence to prioritize alerts from suspicious external sources.

Question: What do attackers scan, other than, IP addresses, ports, and OS version, in order to identify vulnerabilities in a network?

> **Answer:** Services

***

## Task 3: External vs Internal Scanning

External scanning occurs when an outside IP targets public-facing assets. This is considered low-severity reconnaissance.&#x20;

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Internal-to-internal scanning, however, suggests an attacker has already gained a foothold and is moving toward lateral movement (the Discovery phase of MITRE ATT\&CK). This is always a high-severity event requiring incident response.

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

To identify these in the lab, we navigate to `/home/ubuntu/Downloads/logs` and use basic Linux utilities to parse the CSV exports.

```bash
# Previewing the log structure
head -n2 log-session-1.csv

# Using cut to isolate source and destination IPs (skipping the timestamp column)
cut -d ',' -f 2,4 log-session-2.csv | head
```

Question: Which file contains logs that showcase internal scanning activity?

> **Answer:** log-session-2.csv

Question: How many log entries are present for the internal IP performing internal scanning activity?

> **Answer:** 2276

Question: What is the external IP address that is performing external scanning activity?

> **Answer:** 203.0.113.25

***

## Task 4: Horizontal vs Vertical Scanning

There are two primary patterns in port scanning:

<figure><img src="../.gitbook/assets/image (10) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Horizontal Scanning:** Scanning multiple destination IPs for the same port (e.g., looking for port 445/SMB across a whole subnet).

<figure><img src="../.gitbook/assets/image (11) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Vertical Scanning:** Scanning a single host for multiple different ports to footprint the services available on that specific machine.

Question: One of the log files contains evidence of a horizontal scan. Which IP range was scanned? Format X.X.X.X/X

> **Answer:** 203.0.113.0/24

Question: In the same log file, there is one IP address on which a vertical scan is performed. Which IP address is this?

> **Answer:** 192.168.230.145

Question: On one of the IP addresses, only a few ports are scanned which host common services. Which are the ports that are scanned on this IP address? Format: port1, port2, port3 in ascending order.

> **Answer:** 80, 445, 3389

***

## Task 5: The Mechanics of Scanning

Scanning techniques vary in stealth and reliability:

* **Ping Sweep:** Uses ICMP to find online hosts; often blocked by modern firewalls.
* **TCP SYN Scan:** The "half-open" scan. It sends a SYN and waits for a SYN-ACK. If received, the port is open. It is stealthier as it doesn't complete the full handshake.
* **UDP Scan:** Relies on ICMP "destination unreachable" messages for closed ports. It is slow and often unreliable.

In this task, we use Kibana to visualize these mechanics. By filtering for specific `zeek.conn.conn_state` values (like `S0` for SYN attempts), we can pinpoint exactly what the scanner is doing.

Question: Which source IP performs a ping sweep attack across a whole subnet?

> **Answer:** 192.168.230.127

Question: The zeek.conn.conn\_state value shows the connection state. Using the information provided by this value, identify the type of scan being performed by 203.0.113.25 against 192.168.230.145

> **Answer:** TCP SYN Scan

Question: Is there any UDP scanning attempt in the logs? Y/N

> **Answer:** N

***

## Conclusion

Understanding the "why" and "how" of network discovery is a prerequisite for effective detection. By distinguishing between horizontal and vertical scans and monitoring for internal-to-internal reconnaissance, we can catch attackers in the early stages of their lifecycle before they successfully move laterally through the infrastructure.
