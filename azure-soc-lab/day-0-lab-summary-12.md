---
icon: wrench
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 12 - Cowrie Analysis I

**Date:** 06.07.2026

**Core Concept**: Threat Intelligence Analysis via Kibana Query Language

Day 12 focused on using Kibana Query Language to explore accumulated attack telemetry and identify threat patterns. The analysis revealed three distinct attacker categories: automated credential scanners, semi-competent individual actors, and sophisticated multi-stage malware campaigns.

***

## Section 1: KQL Fundamentals and Query Building

Kibana Query Language provides straightforward syntax for filtering and analyzing security events. Basic queries filter by field values:

```kql
eventid: "cowrie.login.success"
eventid: "cowrie.command.input"
src_ip: "45.153.34.144"
username: "admin"
```

<figure><img src="../.gitbook/assets/image (426).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (427).png" alt=""><figcaption></figcaption></figure>

Filters combine with AND/OR logic to trace attack chains:

```kql
eventid: "cowrie.command.input" AND src_ip.keyword: 45.153.34.144
```

<figure><img src="../.gitbook/assets/image (428).png" alt=""><figcaption></figcaption></figure>

This approach along with using the filters in the GUI transformed raw logs into actionable threat intelligence.

***

## Section 2: Attacker Stratification

Analysis of 131+ commands identified three distinct threat levels.

**Tier 1: Automated Credential Scanners (80% of traffic)**

Commands executed repeatedly by multiple IPs:

* `uname -s -v -n -r -m` (24 executions) - System fingerprinting
* `[ -f /proc/version ]` checks (8 executions) - Version detection
* `cd ..` directory traversal (7 executions)
* `ls` basic enumeration (6 executions)

Pattern: These are **dumb bots running identical recon scripts**. Multiple IPs from the 45.153.34.0/24 range followed the same playbook, indicating a shared scanner codebase.

**Tier 2: Semi-Competent Individual Actor (1 session)**

<figure><img src="../.gitbook/assets/image (431).png" alt=""><figcaption></figcaption></figure>

IP 58.235.123.214 executed a different attack pattern:

* `cat /etc/passwd` - User enumeration
* `su root`, `su dbadmin` - Privilege escalation attempts
* `whoami`, `history` - Context verification
* `ping 103.207.85.8` - C2 communication attempt
* `sudo apt install update` - Payload staging

Analysis: This attacker logged in on first attempt (`admin/admin123`), performed structured reconnaissance, attempted to contact a known C2 server, and exited when privilege escalation failed. Either human-directed or a more sophisticated automated tool with decision logic. The single session suggests they recognized the honeypot's constraints or lost interest.

**Tier 3: Sophisticated Multi-Stage Malware**

Two significantly larger command payloads were executed, indicating real threat actor infrastructure.

Command 1: Cloud VM Compromise & Persistence

<figure><img src="../.gitbook/assets/image (429).png" alt=""><figcaption></figcaption></figure>

* Identifies writable directories for payload staging
* **Disables Alibaba/Aliyun security services** (targets Chinese cloud infrastructure)
* Removes file immutability attributes (`chattr -i -a`)
* Downloads second-stage payload from 23.160.56.10
* Renames `wget` to `good` and `curl` to `cool` for evasion
* Clears system logs and firewall rules

Command 2: C2 Fingerprinting Beacon

<figure><img src="../.gitbook/assets/image (430).png" alt=""><figcaption></figcaption></figure>

* Collects structured system data: OS, architecture, uptime, CPU model, GPU, user login history
* Tests shell behavior and error handling
* Formats output for exfiltration: `UNAME:`, `ARCH:`, `CPUS:`, `CPU_MODEL:`, `GPU:`, `LAST:`

Pattern: **GPU enumeration suggests cryptominer targeting**. The attacker profiles systems to select appropriate payloads—high-end GPUs and multi-CPU systems are prioritized for mining operations.

***

## Section 3: Infrastructure Correlation and Malware Association

**45.153.34.X Network**

<figure><img src="../.gitbook/assets/image (433).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (434).png" alt="" width="511"><figcaption></figcaption></figure>

The credential scanning botnet operating from the 45.153.34.0/24 range has been **confirmed as malicious on VirusTotal**. Community members have flagged this IP range as actively distributing malware. Specifically, the network is associated with **destoryed.exe**, a Windows executable detected by 43 out of 71 antivirus engines on VirusTotal. This malware sample communicates directly with the 45.153.34.X range, confirming a coordinated attack infrastructure.

**Attack Infrastructure: Automated Malware Campaign**

<figure><img src="../.gitbook/assets/image (432).png" alt=""><figcaption></figcaption></figure>

Multiple IP addresses (distinct from 45.153.34.X) executed sophisticated multi-stage payloads downloading from **23.160.56.10**. These operators profiled systems for GPU/CPU capabilities, suggesting **cryptominer targeting**. This represents a separate, coordinated campaign from the credential scanners.

**Attack Infrastructure: Individual Actor**

<figure><img src="../.gitbook/assets/image (435).png" alt="" width="563"><figcaption></figcaption></figure>

IP 58.235.123.214 (likely human or semi-autonomous tool) attempted contact with **103.207.85.8** (confirmed C2 by VirusTotal). This represents an isolated incident a single session that exited when escalation failed. It is important to note this IP could potentially be a script kiddie or other form of low-skilled attacker attempting to masquerade as a sophisticated attacker.

**Summary**

Evidence of at least three distinct threat actors:

1. 45.153.34.x credential scanners (automated, associated with destoryed.exe)
2. Multiple IPs running sophisticated malware from 23.160.56.10 (organized, cryptominer-focused)
3. 58.235.123.214 individual actor/semi-automated tool (opportunistic, limited success)

***

## Section 4: MITRE ATT\&CK Framework Mapping

The captured attacks map to the following MITRE ATT\&CK tactics and techniques:

**Reconnaissance & Initial Access**

* T1595: Active Scanning (system enumeration via uname, /proc/version)
* T1592: Gather Victim Host Information (CPU/GPU profiling)

**Credential Access**

* T1110.003: Brute Force - Password Spraying (45.153.34.x attempting weak passwords)
* T1110.004: Brute Force - Credential Stuffing (admin/admin123, root/123456)

**Discovery**

* T1518: Software Discovery (detecting OS and service versions)
* T1526: Cloud Service Discovery (identifying Aliyun/Qcloud protections)
* T1580: Cloud Infrastructure Discovery (profiling CPU/GPU for resource exploitation)
* T1622: Debugger Evasion (testing shell behavior to detect honeypots)

**Lateral Movement / Privilege Escalation**

* T1548.003: Abuse Elevation Control Mechanism (sudo, su root attempts)

**Defense Evasion**

* T1564.010: Indicator Removal - Clear Firewalls (iptables -F)
* T1070.001: Indicator Removal - Clear Logs (clearing wtmp, btmp, lastlog)
* T1036.003: Masquerading - Rename System Utilities (wget→good, curl→cool)
* T1222.002: File and Directory Permissions Modification (chattr -i -a)

**Command and Control**

* T1071.001: Application Layer Protocol - HTTP (downloading from C2)
* T1008: Fallback Channels (wget, curl, python download methods)
* T1071.004: Application Layer Protocol - DNS (potential DNS exfiltration)

**Impact**

* T1496: Resource Hijacking (cryptominer deployment targeting GPU/CPU)

**Summary:** The attacks represent two distinct campaigns: commodity credential stuffing feeding into cryptomining operations, and individual/semi-autonomous exploitation attempts.

**Threat Level: Medium-High**

The honeypot captured evidence of:

* Active credential stuffing campaigns targeting weak SSH passwords
* Sophisticated malware specifically designed for cloud infrastructure
* Infrastructure targeting Chinese cloud providers (Aliyun/Tencent)
* GPU and CPU enumeration for resource-heavy payloads (cryptomining)
* Log and firewall evasion techniques

While the honeypot's isolation prevented actual compromise, the sophistication and targeting specificity indicate **professional threat actor groups**, not just script kiddies.

***

## Conclusion

Day 12 demonstrated how KQL transforms honeypot telemetry into threat intelligence. By filtering, aggregating, and correlating events across 131 unique commands and dozens of attacker IPs, clear patterns emerged: commodity credential scanners feeding into organized malware distribution networks targeting cloud infrastructure for resource exploitation. This analysis provides both portfolio-worthy insights and actionable indicators of compromise for defending real systems.
