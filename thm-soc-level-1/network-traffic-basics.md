---
icon: traffic-light-stop
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/66c44fd9733427ea1181ad58-1759484536298
coverY: 0
coverHeight: 138
---

# Network Traffic Basics

**Date:** 20.04.2026&#x20;

**Room Category:** Walkthrough

**Core Concept:** Understanding the fundamentals of Network Traffic Analysis (NTA), including capture methods, traffic flows, and observational techniques across the TCP/IP stack.

Network Traffic Analysis (NTA) is the process of capturing, inspecting, and analyzing data as it flows across a network. It provides complete visibility into internal and external communications. NTA is a comprehensive discipline involving the correlation of logs, deep packet inspection, and network flow statistics to establish baselines and detect deviations.

***

## Task 1: Introduction

NTA is a critical skill for SOC analysts and security researchers. It allows for navigating vast amounts of network information to distinguish between normal operations and potential threats.

Question: Continue to discover the purpose of network traffic analysis.

> **Answer:** No answer needed

***

## Task 2: Purpose of Network Traffic Analysis

Logs from firewalls or servers often provide metadata (IPs, timestamps, domain names) but lack the actual content of the communication. NTA fills this gap.

**Scenario: DNS Tunneling** An alert identifies unusual DNS queries from a host. Standard logs show subdomains changing rapidly, but they do not show the payload. By inspecting the full packet, a researcher can find C2 commands hidden in TXT records.

```bash
2025-10-03 09:15:23    SRC=192.168.1.16      QUERY=aj39skdm.malicious-tld.com    QTYPE=A      
2025-10-03 09:15:31    SRC=192.168.1.16      QUERY=msd91azx.malicious-tld.com    QTYPE=A     
2025-10-03 09:15:45    SRC=192.168.1.16      QUERY=cmd01.malicious-tld.com       QTYPE=TXT     
2025-10-03 09:15:45    SRC=192.168.1.16      QUERY=cmd01.malicious-tld.com       QTYPE=TXT     
```

**General Uses for NTA:**

* **Performance Monitoring:** Identifying sudden peaks or latency.
* **Abnormality Detection:** Finding deviations from the established baseline.
* **Content Inspection:** Detecting data exfiltration (e.g., DNS tunneling), malicious file downloads over HTTP, or lateral movement.

**SOC Perspective:**

* Detecting malicious activity that bypasses standard signature-based alerts.
* Reconstructing attack timelines during incident response.
* Validating and verifying alerts from other security tools.

Question: What is the name of the technique used to smuggle C2 commands via DNS?

> **Answer:** DNS tunneling

***

## Task 3: Observable Network Traffic

<figure><img src="../.gitbook/assets/image (283).png" alt=""><figcaption></figcaption></figure>

The TCP/IP stack determines what data can be observed. Each layer adds headers that contain specific information necessary for NTA.

**Application Layer:** Contains application headers and the actual payload. For example, an HTTP log might show a GET request for a ZIP file, but only NTA reveals the binary content of that ZIP file.

**Transport Layer (TCP/UDP):** Segments data and adds headers. While firewalls log ports and flags, NTA allows for the inspection of sequence numbers. A sudden jump in sequence numbers can indicate session hijacking.

**Internet Layer (IP):** Adds source and destination IP addresses. NTA is required here to detect fragmentation attacks, where attackers use overlapping byte ranges or tiny fragments to evade IDS detection.

**Link Layer (Ethernet/MAC):** Handles physical addressing. NTA is vital here for detecting ARP poisoning or spoofing, which is often invisible in higher-level logs that do not track gratuitous ARP packets or MAC-to-interface inconsistencies.

```http
HTTP/1.1 200 OK
Date: Mon, 29 Sep 2025 10:15:30 GMT
Server: nginx/1.18.0
Content-Type: application/zip
Content-Length: 10485760
Content-Disposition: attachment; filename="suspicious_package.zip"
Last-Modified: Mon, 29 Sep 2025 09:54:00 GMT
ETag: "5d8c72-9f8a1c-3a2b4c"
Accept-Ranges: bytes
Connection: close

[binary ZIP file bytes follow — 10,485,760 bytes]
```

Question: Look at the HTTP example in the task and answer the following question: What is the size of the ZIP attachment included in the HTTP response? Note down the answer in bytes.

> **Answer:** 10485760

Question: Which attack do attackers use to try to evade an IDS?

> **Answer:** fragmentation

Question: What field in the TCP header can we use to detect session hijacking?

> **Answer:** sequence number

***

## Task 4: Network Traffic Sources and Flows

Traffic is categorized by where it comes from (Sources) and where it is going (Flows).

**Sources:**

* **Intermediary Sources:** Devices through which traffic passes (firewalls, switches, routers). They generate control traffic like OSPF, BGP, or SNMP.
* **Endpoint Sources:** Devices where traffic originates or terminates (servers, workstations, IoT, mobile). These generate the bulk of network bandwidth.

**Flows:**

<figure><img src="../.gitbook/assets/image (284).png" alt=""><figcaption></figcaption></figure>

* **North-South (NS):** Traffic entering or leaving the LAN via a gateway. Typically client-server protocols like HTTPS, DNS, and SMTP.
* **East-West (EW):** Traffic staying within the LAN. This is often where lateral movement occurs during an attack.

**Specific Flow Examples:**

* **HTTPS:** Often involves a web proxy performing TLS (Transport Layer Security) inspection.
* **External DNS:** Internal hosts query an internal DNS server, which then queries external servers on behalf of the host.

<figure><img src="../.gitbook/assets/image (285).png" alt=""><figcaption></figcaption></figure>

* **SMB with Kerberos:** Authentication via the Domain Controller (KDC) occurs before an SMB session is established for file sharing.

<figure><img src="../.gitbook/assets/image (286).png" alt=""><figcaption></figcaption></figure>

Question: Which category of devices generates the most traffic in a network?

> **Answer:** endpoint

Question: Before an SMB session can be established, which service needs to be contacted first for authentication?

> **Answer:** kerberos

Question: What does TLS stand for?

> **Answer:** Transport Layer Security

***

## Task 5: Methods of Observation

There are three primary ways to acquire information for NTA: Logs, Full Packet Capture (FPC), and Network Statistics.

**Logs:** The first entry point. These vary by vendor and protocol (e.g., Syslog, Windows Event Logs, Apache CLF). Logs are generally metadata-heavy and payload-light.

**Full Packet Capture (FPC):** Capturing every bit of a packet.

<figure><img src="../.gitbook/assets/image (287).png" alt="" width="188"><figcaption></figcaption></figure>

* **Network TAP:** A physical device placed inline. It creates a copy of traffic at the link layer with zero performance impact.

<figure><img src="../.gitbook/assets/image (288).png" alt="" width="347"><figcaption></figcaption></figure>

* **Port Mirroring (SPAN):** A software-based approach where a switch port copies traffic to a monitoring port. This can impact performance on high-traffic lines.

**Network Statistics (Metadata):**

<figure><img src="../.gitbook/assets/image (289).png" alt="" width="289"><figcaption></figcaption></figure>

* **NetFlow:** A Cisco-proprietary (initially) protocol that collects flow metadata (C2 detection, lateral movement).
* **IPFIX:** The vendor-neutral successor to NetFlow, offering more flexibility in field selection.

**Tools for Analysis:**

* **Wireshark:** Graphical packet analyzer.
* **TCPdump:** Command-line packet analyzer.
* **IDS/IPS:** Snort, Suricata, and Zeek for automated detection.

Question: What is the flag found in the HTTP traffic in scenario 1?

> **Answer:** THM{FoundTheMalware}

Question: What is the flag found in the DNS traffic in scenario 2?

> **Answer:** THM{C2CommandFound}

***

## Conclusion

Effective Network Traffic Analysis requires a combination of log correlation, flow metadata, and deep packet inspection. Establishing a baseline is the most critical step for identifying the subtle anomalies that indicate a security breach.
