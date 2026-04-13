---
icon: shield
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5e8dd9a4a45e18443162feab/room-content/d43ff804dd897ce1b7394eb387e59302.png
coverY: 0
coverHeight: 138
---

# Introduction to EDR

**Date:** 07.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding Endpoint Detection and Response (EDR) as a deep-level security solution that provides granular visibility, behavioral detection, and remote response capabilities beyond traditional Antivirus.

EDR is the "black box" of an endpoint. While an Antivirus acts as a gatekeeper, an EDR acts as a continuous surveillance system and a rapid response team, recording every process, file, and network connection to catch advanced threats that bypass perimeter defenses.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1).png" alt="" width="362"><figcaption></figcaption></figure>

Modern security requires monitoring endpoints even when they are outside the corporate network. EDR provides the necessary telemetry to detect and respond to these remote threats.

**Learning Objectives:**

* Differentiate EDR from traditional Antivirus (AV).
* Explore the EDR architecture (Agents/Sensors vs. Central Console).
* Analyze EDR telemetry (Processes, Registry, Network).
* Understand automated and manual response capabilities.

Question: I am all set!

> **Answer:** No answer needed

***

## Task 2: What is an EDR?

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

EDR solutions (like CrowdStrike, SentinelOne, or Microsoft Defender) rely on three main pillars:

1. **Visibility:** Detailed logs of process trees, registry changes, and user actions.

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

2. **Detection:** Uses signatures, behavioral analysis, and machine learning to flag anomalies (e.g., a Word doc spawning PowerShell).

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

3. **Response:** Allows analysts to isolate hosts, kill processes, or remotely access shells to remediate threats.

<figure><img src="../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

Question: Which feature of EDR provides a complete context for all the detections?

> **Answer:** Visibility

Question: Which process spawned sc.exe?

> **Answer:** cmd.exe

***

## Task 3: Beyond the Antivirus

**The Analogy:**

* **AV (Antivirus):** Like an **immigration check**. It checks passports against a database of known criminals. If the criminal is new (Zero-day), they get in.
* **EDR:** Like **security officers/CCTV** inside the airport. Even if the person gets past immigration, the EDR monitors their behavior (roaming near restricted areas, leaving bags unattended).

<figure><img src="../.gitbook/assets/image (13).png" alt="" width="563"><figcaption></figcaption></figure>

| Attack Step           | AV Response                       | EDR Response                                      |
| --------------------- | --------------------------------- | ------------------------------------------------- |
| **Malicious Macro**   | Often misses if signature is new. | Flags unusual Winword -> PowerShell relationship. |
| **Process Injection** | Does not monitor memory.          | Detects injection into `svchost.exe`.             |
| **C2 Connection**     | Lacks network visibility.         | Flags unexpected outbound connections.            |

Question: In the given analogy, what presents an AV?

> **Answer:** immigration check

Question: Which legitimate process was hijacked by the attacker in the scenario?

> **Answer:** svchost.exe

Question: Which security solution might mark this activity as clean?

> **Answer:** Antivirus

***

## Task 4: How an EDR Works?

<figure><img src="../.gitbook/assets/image (14).png" alt="" width="375"><figcaption></figcaption></figure>

EDR architecture consists of two main components:

* **Agents (Sensors):** Installed on the endpoint; the "eyes and ears" that collect telemetry.
* **Console:** The "brain" that correlates data, matches it against Threat Intel, and triggers alerts for the analyst.

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Question: Which component of the EDR is responsible for collecting telemetry from the endpoints?

> **Answer:** Agent

Question: An EDR agent is also known as a?

> **Answer:** sensor

***

## Task 5: EDR Telemetry

Telemetry is the raw data pushed from the sensor to the console. Key types include:

* **Process Activity:** Tracking executions and terminations.
* **Network Connections:** Identifying C2 communication or lateral movement.
* **Command Line:** Capturing obfuscated PowerShell or CMD strings.
* **Registry/File Mods:** Monitoring persistence mechanisms or data staging.

Question: Which telemetry data helps in detecting C2 communications?

> **Answer:** Network Connections

Question: Where are the configuration settings of a Windows system primarily stored?

> **Answer:** registry

***

## Task 6: Detection and Response Capabilities

<figure><img src="../.gitbook/assets/image (16).png" alt="" width="352"><figcaption></figcaption></figure>

#### Detection Techniques

* **Behavioral:** Flags "Winword spawning PowerShell".
* **Anomaly:** Flags a registry modification that deviates from the baseline.
* **IOC Matching:** Matches file hashes against Threat Intel feeds.
* **MITRE Mapping:** Automatically maps alerts to Tactics and Techniques.

#### Response Actions

* **Isolate Host:** Cuts the device off from the network to stop lateral movement.
* **Terminate Process:** Stops a specific malicious thread without crashing the whole system.
* **Real-Time Response (RTR):** Provides a remote shell for manual forensics/remediation.

Question: Which feature of the EDR helps you identify threats based on known malicious behaviours?

> **Answer:** IOC Matching

***

## Task 7: Investigate an Alert on EDR

#### Dashboard Overview

The EDR Dashboard shows **55 Active Hosts** with **4 New Detections**. Detection sources are categorized into **Anomaly**, **Behavior**, and **Threat Intel**.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-07 160148.png" alt=""><figcaption></figcaption></figure>

#### Case Studies from Simulation

**1. Initial Access (DESKTOP-HR01)**

<figure><img src="../.gitbook/assets/Screenshot 2026-04-07 160230.png" alt=""><figcaption></figcaption></figure>

* **Detection:** Malicious Office Document.
* **Process Chain:** `explorer.exe` $\rightarrow$ `WINWORD.EXE` $\rightarrow$ `CMD.EXE` $\rightarrow$ `cURL.EXE` $\rightarrow$ `INSTALL.EXE`.
* **Behavior:** WINWORD.EXE spawned CMD after opening a macro-enabled document (`invoice.docm`).

**2. Credential Dumping (WIN-ENG-LAPTOP03)**

<figure><img src="../.gitbook/assets/Screenshot 2026-04-07 160256.png" alt=""><figcaption></figcaption></figure>

* **Detection:** LSASS Memory Access.
* **Process Chain:** `explorer.exe` $\rightarrow$ `syncsvc.exe` $\rightarrow$ `lsass.exe`.
* **Path:** Suspect binary located in `\AppData\Local\Temp\syncsvc.exe`.

**3. Execution from AppData (DESKTOP-DEV01)**

<figure><img src="../.gitbook/assets/Screenshot 2026-04-07 160311.png" alt=""><figcaption></figcaption></figure>

* **Detection:** Execution of user-space binary.
* **Process:** `UpdateAgent.exe`.
* **Threat Intel:** Labeled as a "Known internal IT utility tool" (False Positive check).

Question: Which tool was launched by CMD.exe to download the payload on DESKTOP-HR01?

> **Answer:** CURL.exe

Question: What is the absolute path to the downloaded malware on the DESKTOP-HR01 machine?

> **Answer:** C:\Users\Public\install.exe

Question: What is the absolute path to the suspicious syncsvc.exe on the WIN-ENG-LAPTOP03 machine?

> **Answer:** C:\Users\haris.khan\AppData\Local\Temp\syncsvc.exe

Question: On which URL was the exfiltration attempt being made on WIN-ENG-LAPTOP03?

> **Answer:** https://files-wetransfer.com/upload/session/ab12cd34ef56/dump\_2025.dmp

Question: What was UpdateAgent.exe labelled by Threat Intel on DESKTOP-DEV01?

> **Answer:** Known internal IT utility tool

***

## Conclusion

EDR is a cornerstone of the modern SOC, providing the granular visibility and rapid response capabilities required to fight advanced persistent threats (APTs) and fileless malware. Beyond simple detection, it empowers security teams to conduct proactive threat hunting by searching through historical telemetry for indicators of compromise that may have otherwise remained hidden.
