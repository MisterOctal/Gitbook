---
icon: magnifying-glass
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/6131132af49360005df01ae3/room-content/10f7d168ab59410ddc28a4b1e89fd6d4.png
coverY: 0
coverHeight: 139
---

# Wireshark: Packet Operations

**Date:** 20.04.2026&#x20;

**Room Category:** Walkthrough

**Core Concept:** Fundamentals of packet analysis with Wireshark, focusing on statistical tools, packet filtering principles, and advanced display filters to isolate specific network events.

This research focuses on the second phase of Wireshark analysis: packet operations. After mastering the basics, the next step involves utilizing statistics, protocol breakdowns, and advanced filtering techniques to find specific data points within massive packet captures.

***

## Comprehensive List of Filters Taught

This section catalogs every filter, operator, and expression taught during this analysis for quick reference.

#### Capture Filters (Syntax)

* `tcp port 80` (Capture port 80 traffic)
* Scope modifiers: `host`, `net`, `port`, `portrange`
* Direction modifiers: `src`, `dst`, `src or dst`, `src and dst`
* Protocol modifiers: `ether`, `wlan`, `ip`, `ip6`, `arp`, `rarp`, `tcp`, `udp`

#### Display Filters: Comparison Operators

* `==` (Equal): `ip.src == 10.10.10.100`
* `!=` (Not equal): `ip.src != 10.10.10.100` (Note: Deprecated, `!(ip.src == 10.10.10.100)` is preferred)
* `>` (Greater than): `ip.ttl > 250`
* `<` (Less than): `ip.ttl < 10`
* `>=` (Greater than or equal to): `ip.ttl >= 0xFA`
* `<=` (Less than or equal to): `ip.ttl <= 0xA`

#### Display Filters: Logical Expressions

* `&&` (Logical AND): `(ip.src == 10.10.10.100) && (ip.src == 10.10.10.111)`
* `||` (Logical OR): `(ip.src == 10.10.10.100) || (ip.src == 10.10.10.111)`
* `!` (Logical NOT): `!(ip.src == 10.10.10.222)`

#### Display Filters: IP Protocol

* `ip` (Show all IP packets)
* `ip.addr == 10.10.10.111` (Show all packets containing IP address 10.10.10.111)
* `ip.addr == 10.10.10.0/24` (Show all packets from the 10.10.10.0/24 subnet)
* `ip.src == 10.10.10.111` (Show all packets originating from 10.10.10.111)
* `ip.dst == 10.10.10.111` (Show all packets sent to 10.10.10.111)

#### Display Filters: TCP and UDP Protocols

* `tcp.port == 80` (Show all TCP packets with port 80)
* `udp.port == 53` (Show all UDP packets with port 53)
* `tcp.srcport == 1234` (Show all TCP packets originating from port 1234)
* `udp.srcport == 1234` (Show all UDP packets originating from port 1234)
* `tcp.dstport == 80` (Show all TCP packets sent to port 80)
* `udp.dstport == 5353` (Show all UDP packets sent to port 5353)

#### Display Filters: HTTP and DNS Protocols

* `http` (Show all HTTP packets)
* `dns` (Show all DNS packets)
* `http.response.code == 200` (Show all packets with HTTP response code 200)
* `dns.flags.response == 0` (Show all DNS requests)
* `http.request.method == "GET"` (Show all HTTP GET requests)
* `dns.flags.response == 1` (Show all DNS responses)
* `http.request.method == "POST"` (Show all HTTP POST requests)
* `dns.qry.type == 1` (Show all DNS "A" records)

#### Display Filters: Advanced Functions and Operators

* **contains:** `http.server contains "Apache"` (Search a value inside packets, case-sensitive)
* **matches:** `http.host matches "\.(php|html)"` (Search a regex pattern, case-insensitive)
* **in:** `tcp.port in {80 443 8080}` (Search a value inside a specific scope/range)
* **upper:** `upper(http.server) contains "APACHE"` (Convert string to uppercase for comparison)
* **lower:** `lower(http.server) contains "apache"` (Convert string to lowercase for comparison)
* **string:** `string(frame.number) matches "[13579]$"` (Convert non-string value to string for pattern matching)

***

## Task 1: Introduction

Investigating network traffic requires an understanding of how to summarize data before digging into individual packets. By processing the raw pcap file, a high-level hypothesis can be formulated.

Question: Read the task above.

> **Answer:** No answer needed

***

## Task 2: Statistics | Summary

The Statistics menu provides a macro-level view of the traffic scope, endpoints, and protocol distributions.

**Resolved Addresses:** Provides a list of IP addresses and their DNS hostnames (derived from DNS answers in the capture file). This is critical for quickly identifying accessed resources without manually hunting for DNS packets.

<figure><img src="../.gitbook/assets/image (261).png" alt=""><figcaption></figcaption></figure>

**Protocol Hierarchy:** Breaks down all available protocols into a tree view based on packet counters and percentages. This helps identify the overall usage of ports and services.

<figure><img src="../.gitbook/assets/image (262).png" alt=""><figcaption></figcaption></figure>

**Conversations & Endpoints:**

* **Conversations:** Lists traffic between two specific endpoints (Ethernet, IPv4, IPv6, TCP, UDP).

<figure><img src="../.gitbook/assets/image (263).png" alt=""><figcaption></figcaption></figure>

* **Endpoints:** Lists unique nodes in the capture file.

<figure><img src="../.gitbook/assets/image (264).png" alt=""><figcaption></figcaption></figure>

* **Name Resolution:** MAC addresses can be resolved to manufacturer names. IP and Port name resolution must be activated via Preferences.

<figure><img src="../.gitbook/assets/image (265).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

* **GeoIP Mapping:** Wireshark supports MaxMind databases to map source and destination IP addresses to physical locations, though this requires the database files to be configured in Preferences.

<figure><img src="../.gitbook/assets/image (267).png" alt=""><figcaption></figcaption></figure>

Question: Investigate the resolved addresses. What is the IP address of the hostname starts with "bbc"? Filter: (Accessed via UI: Statistics -> Resolved Addresses)

> **Answer:** 199.232.24.81

Question: What is the number of IPv4 conversations? Filter: (Accessed via UI: Statistics -> Conversations -> IPv4)

> **Answer:** 435

Question: How many bytes (k) were transferred from the "Micro-St" MAC address? Filter: (Accessed via UI: Statistics -> Endpoints -> Ethernet)

> **Answer:** 7474

Question: What is the number of IP addresses linked with "Kansas City"? Filter: (Accessed via UI: Statistics -> Endpoints -> IPv4 (with GeoIP configured))

> **Answer:** 4

Question: Which IP address is linked with "Blicnet" AS Organisation? Filter: (Accessed via UI: Statistics -> Endpoints -> IPv4 (with GeoIP configured))

> **Answer:** 188.246.82.7

***

## Task 3: Statistics | Protocol Details

Beyond general endpoints, Wireshark can isolate statistics by specific protocols.

**IPv4 and IPv6:** The `IPvX Statistics` menu narrows down all events linked to specific IP versions, displaying counts, rates, and percentages.

<figure><img src="../.gitbook/assets/image (268).png" alt=""><figcaption></figcaption></figure>

**DNS:** The `DNS` statistics menu provides a breakdown including rcode, opcode, class, query type, and service stats. This is highly effective for identifying domain anomalies or DNS tunneling attempts.

<figure><img src="../.gitbook/assets/image (269).png" alt=""><figcaption></figcaption></figure>

**HTTP:** The `HTTP` statistics menu breaks down request and response codes, original requests, and load distribution.

<figure><img src="../.gitbook/assets/image (270).png" alt=""><figcaption></figcaption></figure>

Question: What is the most used IPv4 destination address? Filter: (Accessed via UI: Statistics -> IPv4 Statistics -> Destinations)

> **Answer:** 10.100.1.33

Question: What is the max service request-response time of the DNS packets? Filter: (Accessed via UI: Statistics -> DNS)

> **Answer:** 0.467897

Question: What is the number of HTTP Requests accomplished by "rad\[.]msn\[.]com? Filter: (Accessed via UI: Statistics -> HTTP -> Requests)

> **Answer:** 39

***

## Task 4: Packet Filtering | Principles

Filtering is divided into two distinct categories that cannot be used interchangeably.

**Capture Filters:** Used to save only a specific part of the traffic during the sniffing phase. They use byte offsets, hex values, and masks. They must be set before capturing begins and cannot be changed during the process. Example: `tcp port 80`.

<figure><img src="../.gitbook/assets/image (271).png" alt=""><figcaption></figcaption></figure>

**Display Filters:** Used to investigate existing packets by hiding irrelevant traffic. They can be changed dynamically. Example: `tcp.port == 80`.

<figure><img src="../.gitbook/assets/image (272).png" alt=""><figcaption></figcaption></figure>

**Filter Toolbar Features:** The toolbar validates syntax via color coding:

<figure><img src="../.gitbook/assets/image (273).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (274).png" alt=""><figcaption></figcaption></figure>

* **Green:** Valid filter.
* **Red:** Invalid syntax.
* **Yellow:** Warning (works, but is unreliable or uses deprecated syntax like `!=`).

Question: Read the task above.

> **Answer:** No answer needed

***

## Task 5: Packet Filtering | Protocol Filters

This section covers the application of standard display filters across the Network, Transport, and Application layers.

**Display Filter Expressions Menu:** Because memorizing 3000+ protocol filters is impossible, Wireshark provides a "Display Filter Expression" builder under the Analyze menu to assist with finding acceptable fields and values.

Question: What is the number of IP packets? Filter:

```bash
ip
```

> **Answer:** 81420

Question: What is the number of packets with a "TTL value less than 10"? Filter:

```bash
ip.ttl < 10
```

> **Answer:** 66

Question: What is the number of packets which uses "TCP port 4444"? Filter:

```bash
tcp.port == 4444
```

> **Answer:** 632

Question: What is the number of "HTTP GET" requests sent to port "80"? Filter:

```bash
http.request.method == "GET" && tcp.port == 80
```

> **Answer:** 527

Question: What is the number of type A DNS Queries? Filter:

```bash
dns.qry.type == 1 and not llmn
```

> **Answer:** 51

***

## Task 6: Advanced Filtering

Advanced filtering relies on specific functions and regex matching to locate highly specific data points that standard equal/not-equal operators cannot handle.

**Key Advanced Operators:**

* **contains / matches:** For string and regex pattern hunting.

<figure><img src="../.gitbook/assets/image (275).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (276).png" alt=""><figcaption></figcaption></figure>

* **in:** For evaluating against a set of variables.

<figure><img src="../.gitbook/assets/image (277).png" alt=""><figcaption></figcaption></figure>

* **upper / lower / string:** For data type conversion prior to evaluation.

<figure><img src="../.gitbook/assets/image (278).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (279).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (280).png" alt=""><figcaption></figcaption></figure>

**Bookmarks, Buttons, and Profiles:** Complex filters can be saved as bookmarks or added as clickable buttons on the filter toolbar. Furthermore, Wireshark Profiles allow for saving entirely different workspaces (including coloring rules, custom columns, and filter buttons) tailored to specific types of investigations (for example, "Checksum Control").

<figure><img src="../.gitbook/assets/image (281).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (282).png" alt=""><figcaption></figcaption></figure>

Question: Find all Microsoft IIS servers. What is the number of packets that did not originate from "port 80"? Filter:

```bash
http.server contains "IIS" && tcp.srcport != 80
```

> **Answer:** 21

Question: Find all Microsoft IIS servers. What is the number of packets that have "version 7.5"? Filter:

```bash
http.server contains "IIS" && http.server contains "7.5"
```

> **Answer:** 71

Question: What is the total number of packets that use ports 3333, 4444 or 9999? Filter:

```bash
tcp.port in {3333 4444 9999} || udp.port in {3333 4444 9999}
```

> **Answer:** 2235

Question: What is the number of packets with "even TTL numbers"? Filter:

```bash
string(ip.ttl) matches "[02468]$"
```

> **Answer:** 77289

Question: Change the profile to "Checksum Control". What is the number of "Bad TCP Checksum" packets? Filter: (Applied via UI: Edit -> Configuration Profiles -> "Checksum Control", utilizing the built-in bad checksum filter)

```bash
tcp.analysis.bad_checksum
```

> **Answer:** 34185

Question: Use the existing filtering button to filter the traffic. What is the number of displayed packets? Filter: (Applied via UI: Clicking the pre-configured button generated by the Checksum Control profile)

> **Answer:** 261

***

## Conclusion

Wireshark's true power lies in its ability to narrow down millions of packets into a handful of relevant events using a combination of statistical summaries and advanced display filter expressions. This is extremely important as SOC analysts are often met with information overload and too much noise.
