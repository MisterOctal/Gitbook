---
icon: magnifying-glass
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/5e8dd9a4a45e18443162feab-1759866251635
coverY: 0
coverHeight: 138
---

# Man-in-the-Middle Detection

**Date:** 24.04.2026

**Room Category:** Walkthrough

**Core Concept:** Identifying the three-stage attack chain of ARP Spoofing, DNS Spoofing, and SSL Stripping through network traffic analysis and behavioral indicators.

Man-in-the-Middle (MITM) attacks are among the most dangerous network threats because they allow an adversary to intercept, modify, and steal data between two "trusting" endpoints. Detecting these requires identifying protocol anomalies, such as duplicate MAC addresses or unencrypted sensitive data, that deviate from the network baseline.

***

## Task 1: Introduction

A MITM attack positions the attacker as a bridge between a victim and their destination. From a SOC perspective, spotting these early is critical to preventing full credential compromise or payload injection.

Question: Continue to the next room.

> **Answer:** No answer needed

***

## Task 2: Lab Connection

The lab environment provides a PCAP file (`network-traffic.pcap`) and Splunk logs (`index=network_logs`) documenting a chained attack involving ARP, DNS, and SSL manipulation.

Question: Connect with the Lab.

> **Answer:** No answer needed

***

## Task 3: MITM Attacks - An Overview

MITM attacks typically follow a two-step process: **Interception** (inserting themselves into the stream) and **Manipulation** (reading or changing the data). Within the Cyber Kill Chain, MITM serves as both an **Exploitation** technique (abusing protocol trust) and an **Installation** vector (injecting malware into the data stream).

Question: Continue to the next task.

> **Answer:** No answer needed

***

## Task 4: Detecting ARP Spoofing

ARP spoofing exploits the lack of authentication in the ARP protocol. Attackers send forged "is-at" messages to link their MAC address with a legitimate IP, such as the Gateway.

**Wireshark Filters for ARP Investigation:**

```bash
# Isolate all ARP traffic
arp

# Filter for ARP Requests (Opcode 1)
arp.opcode == 1

# Filter for ARP Responses (Opcode 2)
arp.opcode == 2

# Identify Gratuitous ARP responses (unsolicited)
arp.isgratuitous

# Find duplicate MAC address mappings to a single IP
arp.duplicate-address-detected || arp.duplicate-address-frame

# Narrow down on specific Gateway impersonation
arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1
```

Question: How many ARP packets from the gateway MAC Address were observed?

> **Answer:** 10

Question: What MAC address was used by the attacker to impersonate the gateway?

> **Answer:** 02:fe:fe:fe:55:55

Question: How many Gratuitous ARP replies were observed for 192.168.10.1?

> **Answer:** 2

Question: How many unique MAC addresses claimed the same IP (192.168.10.1)?

> **Answer:** 2

Question: How many ARP spoofing packets were observed in total from the attacker?

> **Answer:** 14

***

## Task 5: Unmasking DNS Spoofing

DNS spoofing involves sending fake DNS responses to redirect a victim's traffic to an attacker-controlled IP. The most reliable indicator is seeing multiple DNS responses for the same query: one from the legitimate resolver and one from the rogue host.

**Wireshark Filters for DNS Investigation:**

```bash
# Isolate all DNS traffic
dns

# View legitimate responses from a known resolver (e.g., Google)
dns.flags.response == 1 && ip.src == 8.8.8.8

# Identify responses for a specific internal domain
dns && dns.qry.name == "corp-login.acme-corp.local"

# Find rogue DNS responses (not from the legitimate resolver)
dns.flags.response == 1 && ip.src != 8.8.8.8 && dns.qry.name == "corp-login.acme-corp.local"
```

Question: How many DNS responses were observed for the domain corp-login.acme-corp.local?

> **Answer:** 211

Question: How many DNS requests were observed from the IPs other than 8.8.8.8?

> **Answer:** 2

Question: What IP did the attacker’s forged DNS response return for the domain?

> **Answer:** 192.168.10.55

***

## Task 6: Spotting SSL Stripping in Action

SSL Stripping downgrades a secure HTTPS connection to insecure HTTP. While the attacker maintains an encrypted session with the server, the victim sends their data (like passwords) in plaintext over the wire.

**Wireshark Filters for SSL Investigation:**

```bash
# Isolate TLS/SSL handshake traffic
tls || ssl

# Filter for specific domain TLS handshakes
tls.handshake.type == 1 && tls.handshake.extensions_server_name == "corp-login.acme-corp.local"

# Detect unencrypted HTTP traffic between the victim and the attacker IP
http && ip.src == 192.168.10.10 && ip.dst == 192.168.10.55

# Search for POST requests to identify credential theft
http.request.method == "POST"
```

Question: How many POST requests were observed for our domain corp-login.acme-corp.local?

> **Answer:** 1

Question: What's the password of the victim found in the plaintext after successful ssl stripping attack?

> **Answer:** Secret123!

***

## Task 7: Conclusion

The investigation confirmed a chained MITM attack:

1. **ARP Spoofing:** To intercept traffic at Layer 2.
2. **DNS Spoofing:** To redirect the domain `corp-login.acme-corp.local` to the attacker's IP.
3. **SSL Stripping:** To downgrade the connection and capture the victim's password in plaintext.
