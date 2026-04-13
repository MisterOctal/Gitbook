---
icon: chart-pyramid
cover: https://assets.tryhackme.com/additional/pyramidofpain/updated/banner.png
coverY: 0
coverHeight: 139
---

# Pyramid of Pain

**Date:** 10.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the Pyramid of Pain model to evaluate how difficult it is for an adversary to change specific indicators (IoCs) and how much "pain" we cause them by detecting those indicators.

The Pyramid of Pain is a conceptual model used by security analysts to improve the effectiveness of threat hunting and incident response. It ranks indicators of compromise (IoCs) based on the amount of effort required for an attacker to change them once they are discovered by defenders.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (9).png" alt="" width="188"><figcaption></figcaption></figure>

The Pyramid of Pain is applied in modern solutions like Cisco Security and SentinelOne to improve Cyber Threat Intelligence (CTI).

Question: Read the above.

> **Answer:** No answer needed

***

## Task 2: Hash Values (Trivial)

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

At the base of the pyramid are **Hash Values**. These are unique identifiers for files (MD5, SHA-1, SHA-256). These are relatively easy to solve and analyze, thanks to various sites such as VirusTotal.

* **Why it's "Trivial":** An attacker can change a file's hash by simply adding a single character or bit to the file (e.g., using `echo "..." >> file`).
* **Detection:** While easy to spot using tools like VirusTotal, they are the least durable indicators.

Question: Analyse the report for hash `b8ef959a9176aef07fdca8705254a163b50b49a17217a4ff0107487f59d4a35d`. What is the filename?

> **Answer:** Sales\_Receipt 5606.xls

***

## Task 3: IP Address (Easy)

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

**IP Addresses** are marked green. They identify devices on a network.

* **Adversary Tactic:** Attackers use **Fast Flux** (constantly changing IPs associated with a domain) to bypass IP-based blocking at firewalls.
* **Why it's "Easy":** Changing a public IP or using a different proxy is simple for most adversaries.

Question: What is the first IP address the malicious process (PID 1632) attempts to communicate with?

> **Answer:** 50.87.136.52

Question: What is the first domain name the malicious process (PID 1632) attempts to communicate with?

> **Answer:** craftingalegacy.com

***

## Task 4: Domain Names (Simple)

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Moving to the teal zone, **Domain Names** map IPs to readable text.

* **Punycode Attack:** Using Unicode characters to mimic legitimate domains (e.g., `adıdas.de` vs `adidas.de`).
* **Why it's "Simple":** It requires more effort than changing an IP (registering a domain, modifying DNS), but APIs make this relatively quick for attackers.

Question: What is the first suspicious domain request in the any.run report?

> **Answer:** craftingalegacy.com

Question: What type of attack uses Unicode characters in the domain name to imitate a known domain?

> **Answer:** Punycode attack

Question: Provide the redirected website for: `https://tinyurl.com/bw7t8p4u`

> **Answer:** https://tryhackme.com/

***

## Task 5: Host Artifacts (Annoying)

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

**Host Artifacts** (Yellow zone) are traces left on a system: registry values, dropped files, or specific process patterns.

* **Why it's "Annoying":** If detected, the attacker must re-code their tools or change their installation methodology, which consumes significant time.

Question: A process named `regidle.exe` makes a POST request to a US IP on port 8080. What is the IP?

> **Answer:** 96.126.101.6

Question: How many vendors determine this host to be malicious in the VirusTotal report?

> **Answer:** 9

***

## Task 6: Network Artifacts (Annoying)

<figure><img src="../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

Also in the Yellow zone, these include **User-Agent strings**, URI patterns, or C2 protocols.

* **Tools:** Wireshark, TShark, or Snort logs are used to find these patterns.
* **Impact:** Blocking a custom User-Agent string forces the attacker to modify their communication code.

Question: What browser uses the User-Agent string shown in the screenshot?

> **Answer:** Internet Explorer

Question: How many POST requests are in the screenshot from the pcap file?

> **Answer:** 6

***

## Task 7: Tools (Challenging)

<figure><img src="../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

In the "Challenging" level, we detect the **Tools** the adversary uses (backdoors, maldoc creators, etc.).

* **Defensive Weapons:** Antivirus signatures, YARA rules, and **Fuzzy Hashing** (SSDeep).
* **Fuzzy Hashing:** Used to find similarity between files even if they have minor differences.

Question: Provide the method used to determine similarity between files.

> **Answer:** Fuzzy Hashing

Question: Provide the alternative name for fuzzy hashes without the abbreviation.

> **Answer:** context triggered piecewise hashes

***

## Task 8: TTPs (Tough)

The apex of the pyramid is **TTPs (Tactics, Techniques & Procedures)**. This represents the "how" of the attack, mapped by the MITRE ATT\&CK Matrix.

* **Why it's "Tough":** If you detect their TTPs (like Pass-the-Hash), you force the attacker to invent an entirely new way of operating, which often leads to them giving up.

Question: How many techniques fall under the Exfiltration category in MITRE ATT\&CK?

> **Answer:** 9

Question: What is the name of the tool the China-based group Chimera uses for C2?

> **Answer:** Cobalt Strike

***

## Task 9: Practical: The Pyramid of Pain

The practical exercise requires deploying a static site and categorizing various indicators into the correct tiers of the pyramid. By correctly identifying where specific artifacts fall, you demonstrate a practical understanding of indicator durability.

Question: Complete the static site. What is the flag?

> **Answer:** THM{PYRAMIDS\_COMPLETE}

***

## Conclusion

The Pyramid of Pain is an essential framework for shifting from reactive blocking to proactive adversary disruption. By focusing on higher-level indicators like TTPs, defenders can inflict maximum operational cost on attackers.

### Summary Table

| Tier                  | Color  | Indicator Type     | Difficulty to Change |
| --------------------- | ------ | ------------------ | -------------------- |
| **TTPs**              | Red    | Tactics/Procedures | Tough                |
| **Tools**             | Orange | Software/Scripts   | Challenging          |
| **Network Artifacts** | Yellow | Traffic Patterns   | Annoying             |
| **Host Artifacts**    | Yellow | Registry/Files     | Annoying             |
| **Domain Names**      | Teal   | URLs/DNS           | Simple               |
| **IP Addresses**      | Green  | Network IDs        | Easy                 |
| **Hash Values**       | Blue   | File Fingerprints  | Trivial              |
