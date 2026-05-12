---
icon: chart-column
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/674d9727a22822c1eb46cb31-1755761817571
coverY: 0
coverHeight: 139
---

# Log Analysis with SIEM

**Date:** 12.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Understanding how Security Information and Event Management (SIEM) solutions are utilized to detect, correlate, and analyze malicious behavior across various data sources (Windows, Linux, Web, and Network logs).

Any modern SOC analyst must be able to effectively use a SIEM to analyze and correlate logs while quickly identifying malicious activity. Equally important is understanding the different data sources behind the logs and how each one helps construct the full picture during an investigation.

***

## Task 1: Introduction

This room explores the benefits of SIEMs, the distinct types of logs ingested into them, and the critical importance of log normalization and correlation. Practical exercises involve utilizing Splunk to investigate Windows, Linux, and Web application compromises.

Question: Let's go!

> **Answer:** No answer needed

***

## Task 2: Benefits of SIEM for Analysts

SIEM solutions play a vital role in every Security Operations Center by streamlining the investigation process.

<figure><img src="../.gitbook/assets/image (355).png" alt="" width="188"><figcaption></figcaption></figure>

**Centralisation** A SIEM gathers data from network devices, cloud services, identity providers, and endpoints into one single location. Analysts do not need to log into each system separately (e.g., checking an IPS separately from an EDR), making investigations much smoother and more efficient.

<figure><img src="../.gitbook/assets/image (357).png" alt="" width="375"><figcaption></figcaption></figure>

**Correlation** Correlation is the ability to link separate events together. For example, if an IDS alerts on internal network discovery from a specific IP, a SIEM can automatically correlate that IP with Windows Event Logs to identify exactly which host, user, and process initiated the scan.

<figure><img src="../.gitbook/assets/image (356).png" alt="" width="188"><figcaption></figcaption></figure>

**Historical Events** SIEMs store historical data, allowing analysts to spot patterns or threats that may have started earlier but went unnoticed. Historical logs help identify whether an activity (like an unusual login location) is a known pattern or a genuine anomaly.

Question: What is the process of linking data from multiple sources to identify relationships between individual events called?

> **Answer:** Correlation

Question: What is the process of collecting and storing log data from multiple systems and sources into a single, unified location for easier analysis called?

> **Answer:** Centralisation

***

## Task 3: Log Sources Overview

Logs are collected from various resources acting as the "roots" of the organization's IT structure.

<figure><img src="../.gitbook/assets/image (358).png" alt=""><figcaption></figcaption></figure>

**Host-Based Log Sources:** Collected from individual devices (workstations, servers). Nearly every attack involves hosts in some way, making these logs invaluable for detecting malicious script executions or unauthorized access.&#x20;

<figure><img src="../.gitbook/assets/image (359).png" alt=""><figcaption></figcaption></figure>

**Network-Based Log Sources:** Collected from firewalls, routers, IDS/IPS. They provide visibility into how devices communicate with each other.&#x20;

<figure><img src="../.gitbook/assets/image (360).png" alt=""><figcaption></figcaption></figure>

**Web-Based Log Sources:** Collected from web applications and servers (Apache, Nginx). Careful monitoring here is essential as web vulnerabilities are frequent entry points.

**Time Pitfalls** Logs come from systems in different time zones (UTC, local time). A SIEM often normalizes this display to the analyst's local time zone. It is vital to remain aware of time zone differences to accurately construct timelines.

**Logs Normalisation** Systems log events in various formats (JSON, XML, plain text) using different field names. Normalization converts all these disparate formats into a single, consistent structure. This allows analysts to view, search, and filter everything in one standard way without treating every log source like a unique puzzle.

Question: What is the process of converting logs from different formats into a single format for easier analysis in a SIEM?

> **Answer:** Normalisation

Question: Which log source type can be used to detect the execution of a malicious script?

> **Answer:** Host-Based

***

## Task 4: Windows Logs

When analyzing Windows logs in a SIEM, two primary data sources are combined for maximum visibility: Sysmon and Windows Event Logs (WinEventLogs).

**Sysmon** Provides deep visibility into system behavior. It helps identify malicious process executions, network connections, registry changes, and file creations.

_Splunk Query Example (Malicious Process Execution):_

```bash
index=winenv EventCode=1 *powershell* AND *EncodedCommand*
| table _time ComputerName ParentUser ParentImage ParentCommandLine Image CommandLine
```

_Splunk Query Example (Suspicious Network Connection):_

```bash
index=winenv EventCode=3 ComputerName=WINHOST05
| table _time ComputerName Image SourceIp SourcePort DestinationIp DestinationPort Protocol
```

**WinEventLogs** Includes over 200 distinct log channels, with the **Security** and **System** logs being the most critical.

* **Security Logs:** Detect user authentication attempts, account creation (Event IDs 4720, 4722), and policy changes.
* **System Logs:** Record events generated by the OS and core services. Useful for spotting persistence mechanisms like malicious service creation (Event IDs 7045, 7036).

**Practice Scenario:** Investigate a suspicious network connection using port 5678 on the WIN-105 host.

_Queries Used:_

```bash
# Q1 & Q2: Identify IP and Process
index=task4 destinationip DestinationPort=5678

# Q3 & Q4: Identify MD5 Hash and Scheduled Task
index=task4 "\\Temp\\SharePoInt.exe" md5 | sort _time
```

Question: Which IP address was the connection established with?

> **Answer:** 10.10.114.80

Question: Which process initiated this suspicious connection?

> **Answer:** SharePoInt.exe

Question: What is the MD5 hash of the malicious process from the previous question?

> **Answer:** 770D14FFA142F09730B415506249E7D1

Question: What is the name of the scheduled task that was created on the system?

> **Answer:** Office365 Install

***

## Task 5: Linux Logs

When analyzing Linux systems, two key logs are heavily utilized: `auth.log` and `syslog`.

**Authentication Logs (auth.log)** Tracks user logins, failed attempts, and `sudo` usage. Crucial for identifying brute-force attacks and privilege escalation.

_Splunk Query Example (Brute Force):_

```bash
index=linux source="auth.log" *ubuntu* process=sshd 
| search "Accepted password" OR "Failed password"
```

**System Logs (syslog)** Captures service activity, restarts, and cron jobs. This is the primary location to hunt for persistence mechanisms.

_Splunk Query Example (Cron Persistence):_

```bash
index=linux sourcetype=syslog ("CRON" OR "cron") 
| search ("python" OR "perl" OR "ruby" OR ".sh" OR "bash" OR "nc")
```

**Practice Scenario:** Investigate a potential persistence mechanism involving the creation of a `remote-ssh` user on an Ubuntu server.

_Queries Used:_

```bash
# Q1 & Q2: Identify the remote-ssh account creation and escalating user
index=task5 "remote-ssh"

# Q3: Identify the IP address for the escalating user
index=task5 "jack-brown" | dedup src_ip | table src_ip

# Q4: Identify failed login attempts
index=task5 source="auth.log" "Failed password" user=jack-brown NOT "message repeated"

# Q5: Identify cron persistence and associated port
index=task5 sourcetype=syslog ("CRON" OR "cron")
```

Question: What was the timestamp of the remote-ssh account creation?

> **Answer:** 2025-08-12 09:52:57

Question: Which user successfully escalated their privileges to root prior to the action from the first question?

> **Answer:** jack-brown

Question: From which IP address did the user from the previous question successfully log in to the system?

> **Answer:** 10.14.94.82

Question: How many failed login attempts occurred prior to this successful login?

> **Answer:** 4

Question: Which port is the persistence mechanism configured to connect to?

> **Answer:** 7654

***

## Task 6: Web Application Logs

Web server logs (from Apache, Nginx, etc.) are crucial, as web vulnerabilities are frequent entry points. Access logs identify scanning, DDoS attempts, brute force attacks, and web shells.

**Common Scenarios:**

* **Brute Force Activity:** Look for high volumes of POST requests to login endpoints (e.g., `/wp-login.php`) within a short timeframe.
* **Web Shells:** Search for requests to script files (`.php`, `.jsp`, `.exe`) returning HTTP `200 OK` statuses, usually appearing in sparse bursts from unique user-agents.
* **DDoS Activity:** Look for massive request volumes resulting in `503 Service Unavailable` status codes.

**Practice Scenario:** Investigate a spike in activity on the organization's web server. _(Note: This challenge was solved rapidly without requiring complex queries by simply analyzing the Splunk sidebar statistics under `index=task6`.)_

Question: Which URI path had the highest number of requests?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-12 075332.png" alt="" width="370"><figcaption></figcaption></figure>

> **Answer:** /wp-login.php

Question: Which IP address was the source of the activity?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-12 075359.png" alt="" width="369"><figcaption></figcaption></figure>

> **Answer:** 10.10.243.134

Question: How can this activity be classified?

> **Answer:** Brute Force

Question: Which tool did the threat actor use?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-12 075441.png" alt="" width="372"><figcaption></figcaption></figure>

> **Answer:** WPScan

***

## Conclusion

This room provided a solid foundation on key log sources within SIEM platforms and their value during investigations. Analyzing logs from Windows, Linux, and Web environments allows SOC analysts to correlate events, normalize raw data, and effectively triage alerts.
