---
icon: shield
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/5e8dd9a4a45e18443162feab-1758766604446
coverY: 0
coverHeight: 138
---

# Network Security Essentials

**Date:** 21.04.2026&#x20;

**Category:** Walkthrough&#x20;

**Core Concept:** Fundamentals of network security monitoring, focusing on network visibility, perimeter defense, and log analysis to detect adversary activities.

Networks serve as the backbone of modern organizations. Servers, workstations, applications, and security devices do not exist in isolation; they are interconnected ecosystems. The network perimeter segregates the internal ecosystem from the external Internet and is typically the primary target for attackers. This document outlines the structural components of networks and the critical importance of perimeter monitoring for early threat detection.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (12) (1).png" alt="" width="150"><figcaption></figcaption></figure>

Understanding the foundational concepts of network security from a defensive perspective is crucial. Before diving into log analysis and data sources, a strong understanding of how networks are structured and why perimeter monitoring is necessary must be established.

Question: Complete the task.

> **Answer:** No answer needed

***

## Task 2: Lab Connection

The lab environment provides access to perimeter logs. All required files are located in the `Perimeter_logs` folder, and a Splunk instance is available for centralized log analysis.

Question: Connect to the Lab.

> **Answer:** No answer needed

***

## Task 3: A Network Overview

A computer network is an organized structure where assets connect to enable communication and resource sharing. Knowing the function and security context of each component helps rapidly identify suspicious activity.

<figure><img src="../.gitbook/assets/image (13) (1).png" alt="" width="375"><figcaption></figcaption></figure>

**User Workstations (Endpoints)** Employees utilize workstations (PCs, laptops) for daily tasks. They are the most common entry points for attackers via phishing or malicious downloads.

* **Importance:** While endpoints are sometimes less monitored, a compromise here provides a foothold for lateral movement. Endpoint logs reveal malicious processes, whereas network logs may first expose Command & Control (C2) connections.

**File & Database Servers** These servers store critical business data. File servers centralize shared documents, while database servers manage structured records (customer data, HR, financial data).

* **Importance:** Attackers target these assets to access sensitive data. Ransomware operators target file servers to maximize impact, and data exfiltration campaigns stealthily move data from these servers out of the network.

**Application Servers (Web, Email, VPN, etc.)** These servers host essential services:

* Web Servers: Host websites and web applications.
* Email Servers: Handle corporate communications.
* VPN Gateways: Allow secure remote access.
* **Importance:** As externally facing assets, application servers are high-value targets. Constant scanning for software vulnerabilities or weak configurations occurs here. Monitoring application logs, firewall alerts, and IDS signatures is necessary to identify SQL injections, brute-force attempts, and suspicious external interactions.

**Active Directory (AD) / Authentication Servers** AD is the identity backbone, managing users, groups, computers, and access rights.

* **Importance:** AD controls all user accounts. Attackers target it for privilege escalation, persistence, and lateral movement. A compromised domain admin account can compromise the entire enterprise. Monitoring authentication logs for password spraying, unusual logins from external IPs, or accounts accessing unauthorized systems is critical.

**Routers & Switches (Network Infrastructure)** Routers link different networks (e.g., LAN to the Internet), while switches connect devices within the same network.

* **Importance:** If compromised, these devices allow attackers to intercept and manipulate traffic (Man-in-the-Middle attacks), create backdoors, and open hidden channels.

**Firewalls / Perimeter Devices** Firewalls are security gateways that control traffic between the trusted internal network and the untrusted Internet. They inspect packets and enforce security rules.

* **Importance:** Firewalls protect against direct exposure, prevent unauthorized access to internal services, and log every connection attempt. These logs are often the earliest indicators of port scans, brute-force attacks, or exploitation attempts.

Question: Continue to the Next task.

> **Answer:** No answer needed

***

## Task 4: Network Visibility

Network visibility is the ability to monitor and understand network activity. It is impossible to defend an unseen network. Visibility enables anomaly detection, incident investigation, proactive threat hunting, and compliance verification. This visibility is achieved through two primary log sources.

**Host-Centric Logs** Generated by individual devices (servers, workstations), providing a detailed view of machine-level activity.

<figure><img src="../.gitbook/assets/image (14) (1).png" alt="" width="188"><figcaption></figcaption></figure>

* **OS Logs:** Windows Event Logs, Linux syslog, macOS logs (logons, process creation).
* **Application Logs:** Web servers (Apache, Nginx), databases.
* **Security Tool Logs:** Antivirus, EDR, HIDS.
* **Importance:** Vital for detailed forensic analysis, process tracking, user activity monitoring, and malware impact assessment.

**Network-Centric Logs** Generated by network appliances monitoring traffic flow between devices. They provide context on source and destination IPs, ports, protocols, and actions taken.

<figure><img src="../.gitbook/assets/image (15) (1).png" alt="" width="188"><figcaption></figcaption></figure>

* **Firewalls:** Records of allowed or denied connections based on security rules.
* **IDS/IPS:** Monitors traffic for malicious signatures or anomalous behavior.
* **Routers and Switches:** Generate flow data summarizing traffic conversations.
* **Web Proxies:** Record website visits, providing visibility into web-based threats and exfiltration.
* **VPN:** Tracks remote access connections.
* **Importance:** Essential for early threat detection at the edge, identifying C2 communication, tracking lateral movement, detecting data exfiltration, and providing broad context for an attack.

Question: Continue to the next task.

> **Answer:** No answer needed

***

## Task 5: Network Perimeter

The network perimeter is the boundary separating the trusted internal network from the untrusted external Internet. It is the controlled entry and exit point for all data.

<figure><img src="../.gitbook/assets/image (17) (1).png" alt="" width="300"><figcaption></figcaption></figure>

**Perimeter Components:**

* **Firewalls:** Gatekeepers filtering traffic.
* **Routers/Gateways:** Devices routing traffic and enforcing access rules.
* **Demilitarized Zone (DMZ):** A buffer segment hosting public-facing servers (web, mail, VPN).
* **Remote Access Gateways / VPNs:** Secure entry points for remote workers.

**Importance:** The perimeter is the first line of defense. Attackers constantly scan perimeter IPs for open ports. A weak perimeter leads to unauthorized access of exposed services (RDP, SSH, SMB), data breaches, and malware infiltration. Monitoring involves reviewing firewall logs for blocked/allowed connections, identifying scanning or brute-force attempts, and flagging unusual outbound traffic.

Question: Continue to the next task.

> **Answer:** No answer needed

***

## Task 6: Network Perimeters: Monitoring and Protecting

Monitoring the perimeter allows for the detection of early-stage attacks, misconfigurations, and outbound anomalies. The following scenarios demonstrate perimeter monitoring in action.

**Scenario 1: Probing for Ports (Port Scanning)** An external IP attempts rapid connections to multiple ports on a single internal machine.

```bash
2025-09-22 08:30:04 ALLOW TCP 198.51.100.45:49876 -> 10.0.0.51:80
2025-09-22 08:30:05 BLOCK TCP 203.0.113.10:50001 -> 10.0.0.20:21
2025-09-22 08:30:06 BLOCK TCP 203.0.113.10:50002 -> 10.0.0.20:22
2025-09-22 08:30:07 ALLOW TCP 192.0.2.115:51235 -> 10.0.0.50:443
2025-09-22 08:30:08 BLOCK TCP 203.0.113.10:50003 -> 10.0.0.20:23
2025-09-22 08:30:09 BLOCK TCP 203.0.113.10:50004 -> 10.0.0.20:25
2025-09-22 08:30:10 ALLOW TCP 198.51.100.92:51111 -> 10.0.0.50:443
2025-09-22 08:30:11 BLOCK TCP 203.0.113.10:50005 -> 10.0.0.20:53
```

_Verdict:_ The IP `203.0.113.10` is conducting a classic port scan.

**Scenario 2: Attacking the Web Server (SQL Injection & XSS)** A Web Application Firewall (WAF) logs blocked malicious requests targeting a web server.

```bash
timestamp=2025-09-22T09:14:44Z src_ip=192.0.2.130 action=ALLOW request="GET /index.html"
timestamp=2025-09-22T09:14:45Z src_ip=198.51.100.92 action=ALLOW request="GET /products.php?id=9"
timestamp=2025-09-22T09:14:46Z src_ip=198.51.100.12 action=BLOCK request="GET /search.php?q=<script>alert('XSS')</script>" rule_id=941100 attack_type="XSS"
timestamp=2025-09-22T09:14:47Z src_ip=192.0.2.140 action=ALLOW request="GET /css/style.css"
timestamp=2025-09-22T09:15:42Z src_ip=198.51.100.12 action=BLOCK request="GET /../../../../etc/passwd" rule_id=930120 attack_type="Directory Traversal"
```

_Verdict:_ The IP `198.51.100.12` is actively targeting the website with XSS and Directory Traversal attacks.

**Scenario 3: Guessing the Password (VPN Brute-Force)** VPN logs reveal massive volumes of authentication failures from a single source.

```bash
2025-09-22 10:12:11 FAILED_AUTH TCP 45.137.22.13:31245 -> 10.0.0.1:443 (user 'admin')
2025-09-22 10:12:15 FAILED_AUTH TCP 45.137.22.13:31248 -> 10.0.0.1:443 (user 'admin')
2025-09-22 10:12:21 SUCCESS_AUTH TCP 198.51.100.88:41233 -> 10.0.0.1:443 (user 'b.jones')
2025-09-22 10:12:08 FAILED_AUTH TCP 45.137.22.13:31249 -> 10.0.0.1:443 (user 'guest')
2025-09-22 10:12:09 FAILED_AUTH TCP 45.137.22.13:31250 -> 10.0.0.1:443 (user 'user')
```

_Verdict:_ The IP `45.137.22.13` is conducting a brute-force attack against the VPN gateway.

Question: Examine the firewall logs. Which IP address is performing the port scan?

> **Answer:** 203.0.113.10

Question: In the WAF Logs, which single source IP is responsible for all the blocked web attacks?

> **Answer:** 198.51.100.12

Question: In the VPN logs, how many brute-force attempts failed?

> **Answer:** 90

Question: Which suspicious IP address was found attempting the brute-force attack against the VPN gateway?

> **Answer:** 45.137.22.13

***

## Task 7: Perimeter Logs: Investigating the Breach

An investigation of one month of perimeter logs (Firewall, WAF/IDS, and VPN) for Initech Corp. While manual command-line utilities (grep, awk, sort) are documented below for reference, Splunk was utilized for this investigation using the `index="network_logs"` query, as it provided a highly efficient method to correlate the events without requiring complex filters.

**Manual Analysis Reference (Reconnaissance):**

```bash
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat firewall.log | grep "BLOCK" | cut -d' ' -f5 | cut -d: -f1 | sort -nr | uniq -c
279 [REDACTED]
46 203.0.113.10
26 [REDACTED]
```

**Manual Analysis Reference (VPN Brute-force):**

```bash
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat vpn_auth.log | grep FAIL | cut -d' ' -f3 | sort -nr | uniq -c
118 [REDACTED]
1 203.0.113.100
1 198.51.100.92
1 198.51.100.45
```

**Manual Analysis Reference (Lateral Movement & C2 Beaconing):** Filtering IDS alerts for SMB exploits and C2 beaconing patterns.

```bash
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat ids_alerts.log | grep -n [REDACTED] | grep 'SMB' | cut -d' ' -f6,7,8,9,10,19,21 | head
EXPLOIT Possible MS-SMB Lateral Movement [REDACTED]:2001 [REDACTED]:445
...
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat ids_alerts.log | grep -n [REDACTED] | cut -d' ' -f6,7,8,9,10,19,22,23 | uniq -c | sort -nr | head 
32 TROJAN Possible C2 Beaconing [**] {TCP} [REDACTED]:4444
```

**Manual Analysis Reference (Data Exfiltration):**

```bash
ubuntu@tryhackme:~/Desktop/Perimeter_logs/challenge$ cat ids_alerts.log | grep [REDACTED] | tail
2025-09-27 07:00:00 [**] [1:2002050:1] ET INFO Possible HTTP POST Large Upload [**] [Classification: Potential Data Exfiltration] [Priority: 2] {TCP} [REDACTED]:40050 -> [REDACTED]:8080
```

By querying the respective logs via Splunk, the attack timeline was successfully mapped out from the initial scan to exfiltration.

Question: Examine the firewall logs. What external IP performed the most reconnaissance?

> **Answer:** 203.0.113.45

Question: In the firewall log, Which internal host was targeted by scans?

> **Answer:** 10.0.0.20

Question: Which username was targeted in VPN logs?

> **Answer:** svc\_backup

Question: What internal IP was assigned after successful VPN login?

> **Answer:** 10.8.0.23

Question: Which port was used for lateral SMB attempts?

> **Answer:** 445

Question: In the IDS logs, which host beaconed to the C2?

> **Answer:** 10.0.0.60

Question: During the investigation, which IP was observed to be associated with C2?

> **Answer:** 198.51.100.77

Question: Which host showed the exfiltration attempts?

> **Answer:** 10.0.0.51

***

## Conclusion

An enterprise network is a complex ecosystem. The network perimeter acts as the boundary between trusted and untrusted networks. Attackers continuously test this boundary with port scans, brute-force attempts, and exploits against exposed services.

Monitoring the perimeter allows for the detection of these early signs before adversaries can move deeper into the network. The role of a security analyst involves recognizing normal versus suspicious traffic, escalating unusual activity, and understanding the broader picture of enterprise defense.
