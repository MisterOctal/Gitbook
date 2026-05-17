---
icon: inbox-full
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/5fc2847e1bbebc03aa89fbf2-1755612149483
coverY: 0
coverHeight: 138
---

# IP and Domain Threat Intel

**Date:** 10.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Enriching IP and domain insights using open-source threat intelligence, DNS analysis, geolocation, service exposure, and reputation scoring to accurately triage SOC alerts.

While Security Operations runbooks often rely on the verification and enrichment of file hashes, investigating a lone IP address or domain requires a different approach. Analysts must pivot on geolocation, Autonomous System Numbers (ASNs), open-service footprints, and passive DNS to determine whether a connection represents routine internet traffic or an active adversary foothold.

***

## Task 1: Introduction

This room outlines the process of triaging suspicious domains and IP addresses by applying threat intelligence context. The scenario involves investigating flagged domains (e.g., `advanced-ip-sccanner[.]com`) and IPs, enriching them, and formulating actionable, time-bound security recommendations.

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: All set to begin.

> **Answer:** No answer needed

***

## Task 2: IP Building Blocks

The Domain Name System (DNS) is a central mechanism for internet routing and a frequent target for adversarial abuse. For SOC analysts, DNS provides one of the richest early-warning datasets long before malware payloads are identified.

<figure><img src="../.gitbook/assets/image (5) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

**Core DNS Records for Triage:**

* **A / AAAA Records:** Maps the domain to IPv4/IPv6 addresses. Multiple, rapidly changing A records suggest malicious rotation.
* **NS Records:** Identifies the nameservers controlling the domain. Unusual or recently changed entries can indicate a fresh, malicious setup.
* **MX Records:** Defines email handling servers. Used by attackers to deliver phishing campaigns directly.
* **TXT Records:** Stores SPF and DKIM rules. Poorly configured SPF increases risk in email-related investigations.
* **SOA Record:** Points to the zone's primary authority and includes contact information/serial numbers to support ownership analysis.
* **TTL (Time To Live):** Indicates how long resolvers should cache answers. Extremely low TTLs (seconds/minutes) facilitate rapid infrastructure changes and act as a strong clue for malicious activity.

**Attack Techniques Using DNS:**

* **Fast Flux Hosting:** Adversaries rapidly rotate many IPs with short cache times to evade simple blocking controls.
* **CDN Abuse:** Attackers hide behind legitimate Content Delivery Networks (CDNs) like Cloudflare or Akamai.
* **Typosquatting:** Domains visually spoofing trusted brands (e.g., `micros0ft[.]net` or `paypa1[.]com`).
* **IDN (Internationalised Domain Names):** Attackers exploit Unicode to create Punycode look-alike domains (e.g., `xn--ppaypal-3ya[.]com`).

Question: From the downloadable report, what are the IP addresses for the A Record associated with our flagged domain, advanced-ip-sccanner\[.]com? Answer: IP-1, IP-2.

> **Answer:** 172.67.189.143,104.21.9.202

Question: What nameserver addresses are associated with the IP address? Defang the addresses.

> **Answer:** jaziel\[.]ns\[.]cloudflare\[.]com, summer\[.]ns\[.]cloudflare\[.]com

***

## Task 3: IP Enrichment: Geolocation and ASN

A standalone IP address is ambiguous. Enrichment adds ownership, ASN, geolocation, and service context to ensure blocks are evidence-driven and do not cause collateral damage.

**The Role of RDAP:** The Registration Data Access Protocol (RDAP) is the authoritative source for IP ownership, maintained by Regional Internet Registries (RIRs) like RIPE NCC, ARIN, and APNIC. It provides:

* **NetRange:** The delegated range of addresses.
* **Organisation:** The registered entity holding the IP block.
* **Remarks:** Details on whether the block is used for hosting, broadband, or mobile.
* **Abuse Contact:** The official incident reporting mailbox.

<figure><img src="../.gitbook/assets/image (6).png" alt="" width="563"><figcaption></figcaption></figure>

**Autonomous Systems (ASN) and Heuristics:** An Autonomous System (AS) is a collection of IP prefixes under a single organization's control, assigned a unique ASN.

* **Hosting ASNs:** Feature small netblocks with diverse tenants; frequently host suspicious domains.
* **Residential ISPs:** Massive ranges covering millions of users. Alerts here typically indicate compromised consumer devices (e.g., AS124888 - Vodafone).
* **Cloud/CDN ASNs:** Global anycast infrastructure. Blocking these entirely causes massive collateral damage (e.g., AS16509 - Amazon AWS, AS32934 - Facebook/Meta).

**Geolocation Limitations:** While GeoIP tools (ipinfo.io, iplocation.net) provide country and city estimates, they are often inaccurate. Cloud providers register ranges in one country but host edges globally. SOC analysts must never justify a network block based purely on city-level geolocation data.

Question: Open client.rdap.org and identify when the 64\[.]31\[.]63\[.]194 IP was logged for registration. Answer in UTC: MM/DD/YYYY, H:MM:SS AM/PM

> **Answer:** 12/27/2010, 3:51:03 PM

Question: What roles are assigned to the entity Entity NOC2791-ARIN associated with the IP address? Note: Answer via comma, in alphabetical order.

> **Answer:** administrative,technical

Question: What is the country's name for the same IP address (64\[.]31\[.]63\[.]194)?

> **Answer:** France

Question: Can you identify the Autonomous System linked with the same IP address?

> **Answer:** AS136258

***

## Task 4: Service Exposure

Identifying exposed services provides critical insight into a system's intent and potential blast radius.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Shodan Reconnaissance:** Shodan indexes internet-connected devices, revealing open ports, running services, and system configurations. Analyzing service banners provides hints on server types, frameworks, and operator markers.

**TLS Certificate Transparency (crt.sh):** Certificate Transparency logs publicly trusted certificates, offering a gold mine of enrichment data:

* **Issuer:** Details who signed the certificate (e.g., Let's Encrypt vs. a self-signed cert).
* **Validity Period:** Short-lived certificates (90 days) are normal, but bursts of reissued certificates indicate potential phishing infrastructure.
* **Subject Alternative Names (SANs):** Reveals all domains covered by the certificate, helping uncover related infrastructure.

**Censys Search:** Censys acts as a powerful alternative to Shodan, indexing exposed services across non-standard ports (e.g., exposing an SSH service on port 56003).

Question: Using shodan.io, what is the first exposed service name of the 85\[.]188\[.]1\[.]133 IP? Note: If the information in Shodan has been changed, please check out the hint.

> **Answer:** FTP

Question: How many ports have been identified as open on the server from Question 1? Note: If the information in Shodan has been changed, please check out the hint.

> **Answer:** 6

Question: Using search.censys.io, what is the TLS certificate fingerprint for the IP address? Note: If the information in Censys has been changed, please check out the hint.

> **Answer:** 5ea8e6046bdabaa8e23a1a012c01d1be5ccd42c66ef2577a59f3b3f0f056d12e

Question: According to crt.sh, what is the Subject's commonName of the identified TLS certificate? Note: Search for the TLS fingerprint you identified in Question 3.

> **Answer:** archive.scene.org

***

## Task 5: Reputation Checks and Passive DNS

Unlike static file hashes, IPs and domains are dynamic. A domain hosting a phishing kit today might be parked or benign next week. Time context is critical.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**Reputation Services:**

* **VirusTotal:** Provides detection ratios and historical indicator relations.
* **Cisco Talos Intelligence:** Supplies updated web/email reputation scores, vulnerability information, and traffic volume categorization.
* **IP2Proxy:** Identifies VPNs, proxies, and Tor exit nodes, which act as shared egress points and weaken direct attribution.

**Passive DNS & Historical Context:** Passive DNS records how domains resolved over time:

* **First Seen/Last Seen:** Determines if a domain is newly registered or long-lived.
* **Number of IPs in Time Window:** High churn indicates flux or agile malicious hosting.
* **ASN Spread:** IPs belonging to numerous unrelated ASNs suggest suspicious activity.
* **Wayback Machine:** Reveals historical content shifts (e.g., a domain changing from a benign blog to a credential harvesting site).

Question: What file has been linked to the IP 166\[.]1.160\[.]118?

> **Answer:** ff4c287c60ede1990442115bddd68201d25a735458f76786a938a0aa881d14ef.exe

Question: What organisation is identified on historical WHOIS lookups?

> **Answer:** Ace Data Centers, Inc

***

## Task 6: Operational Integration

Applying threat intelligence correctly avoids breaking legitimate business applications via overly broad controls.

<figure><img src="../.gitbook/assets/image (9).png" alt="" width="375"><figcaption></figcaption></figure>

**Safe Integration Patterns:**

* **Prefer hostnames over IPs:** IPs change frequently on CDNs. Implement DNS response policy zones or SNI filtering instead.
* **Use narrow IPs for single-purpose VPS:** Apply `/32` blocks when an IP is dedicated strictly to staging or C2 activity.
* **Set expiry on blocks:** Infrastructure is frequently recycled. Implementing a 7 to 14-day expiry prevents permanent collateral damage.
* **Document evidence in SOAR:** Include RDAP events, certificate excerpts, and logical reasoning for the block.

**Integration Pitfalls:**

* **Geofencing Cautions:** Wide country blocks often disrupt legitimate remote workers or third-party services. Geolocation should prioritize risk scoring, not primary blocking.
* **Cloud/CDN Pitfalls:** Never block entire AWS, Microsoft, or Cloudflare IP ranges. Doing so invariably breaks business-critical systems. Take action at the domain or path level instead.

Question: I'm ready to move to the final challenge.

> **Answer:** No answer needed

***

## Task 7: Challenge

This practical exercise entails triaging an alert involving a polished phishing campaign targeting Finance, redirecting to `santagift[.]shop`, and concurrent EDR beacons to `170[.]130[.]202[.]134`. The skills established in the previous tasks must be utilized to enrich these indicators.

Question: What is the RIR associated with 170\[.]130\[.]202\[.]134?

> **Answer:** ARIN

Question: What ASN is the IP connected with?

> **Answer:** AS62904

Question: Identify the number of NS records for the domain santagift\[.]shop.

> **Answer:** 4

Question: Which NS is identified as the Start of Authority (SOA) for the domain?

> **Answer:** ns-298.awsdns-37.com

Question: When was the domain registered? (Answer: DD/MM/YYYY)

> **Answer:** 30/10/2022

***

## Conclusion

Enriching and transforming raw IPs and domains into actionable intelligence is a core SOC competency. By leveraging RDAP for ownership, ASNs to evaluate risk, DNS patterns to identify fluxing infrastructure, and certificate/banner analysis to infer purpose, analysts can issue high-confidence, temporary blocks that protect the enterprise without causing operational collateral damage.
