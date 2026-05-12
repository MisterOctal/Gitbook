---
icon: magnifying-glass
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/674d9727a22822c1eb46cb31-1760348150356
coverY: 0
coverHeight: 138
---

# Alert Triage With Splunk

**Date:** 12.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Utilizing Splunk to triage SIEM alerts, effectively investigating brute-force attacks, system persistence mechanisms, and web shell interactions across various environments.

As a SOC analyst, effectively investigating different types of suspicious activity across a variety of assets is a fundamental skill. Knowing exactly what to look for within a SIEM and understanding which log details matter most forms the core of alert triage.

***

## Task 1: Introduction

This room provides practical scenarios simulating a SOC environment. The objective is to analyze triggered alerts, structure Splunk queries to follow the attacker's footprint, and confidently classify the events as True Positives.

**Learning Objectives:**

* Properly investigate alerts in a SOC environment.
* Investigate brute-force attacks on Linux systems.
* Discover persistence mechanisms on Windows systems.
* Analyze a web shell on a vulnerable web server.

Question: Let's go!

> **Answer:** No answer needed

***

## Task 2: Initial Access Alert

**Alert Details:**

* **Alert Name:** Brute Force Activity Detection
* **Target Host:** `tryhackme-2404`
* **Source IP:** `10.10.242.248`

The investigation begins by noting that the source IP is internal, suggesting the attacker has already breached the perimeter. We query the `linux-alert` index to determine if this brute-force activity is genuine and whether it resulted in a successful compromise.

**Splunk Investigation Queries:**

1.  **Identifying Brute Force Count:** Count the failed login attempts grouped by user to identify the targeted account.

    ```bash
    index=linux-alert src_ip=10.10.242.248 action=failure
    | stats count by user
    ```
2.  **Determining Attack Duration:** Find the start and end times of the failed login attempts. Using `| reverse` helps identify the earliest timestamp.

    ```bash
    index=linux-alert src_ip=10.10.242.248 action=failure
    | table _time, user
    | reverse
    ```
3.  **Investigating Privilege Escalation:** Search for successful actions following the brute force to identify session transitions (e.g., escalating to `root`).

    ```bash
    index=linux-alert john.smith action!=failure
    ```
4.  **Detecting Persistence (User Creation):** Look for logs related to the root user creating new accounts on the host for future backdoor access.

    ```bash
    index=linux-alert tryhackme-2404 root
    ```

Question: How many failed login attempts were made on the user john.smith?

> **Answer:** 500

Question: What was the duration of the brute force attack in minutes?

> **Answer:** 5

Question: What username was the attacker able to privilege escalate to?

> **Answer:** root

Question: What is the name of the user account created by the attacker for persistence?

> **Answer:** system-utm

***

## Task 3: Persistence Alert

**Alert Details:**

* **Alert Name:** Potential Task Scheduler Persistence Identified
* **Host:** `WIN-H015`
* **User:** `oliver.thompson`
* **Task Name:** `AssessmentTaskOne`

By examining the task's properties, it is revealed that the task uses `certutil` to download `rv.exe` from an external domain, renames it to `DataCollector.exe`, and executes it via PowerShell. This is a clear indicator of persistence.

**Splunk Investigation Queries:**

1.  **Initial Alert Triage:** View the scheduled task creation (Event ID 4698) and associated process creation (Event ID 1) to identify the parent process.

    ```bash
    index=win-alert WIN-H015 oliver.thompson AssessmentTaskOne
    ```
2.  **Identifying Discovery Activity:** Find local group enumeration commands executed by the attacker (e.g., using the `net` command).

    ```bash
    index=win-alert WIN-H015 net.exe
    ```
3.  **Identifying Origin Workstation:** Identify the source IP and hostname of the remote login to track lateral movement.

    ```bash
    index=win-alert WIN-H015 oliver.thompson action=success logon
    ```

Question: What is the ProcessId of the process that created this malicious task?

> **Answer:** 5816

Question: What is the name of the parent process for the process that created this malicious task?

> **Answer:** cmd.exe

Question: Which local group did the attacker enumerate during discovery?

> **Answer:** Administrators

Question: What is the name of the workstation from which the Threat Actor logged into this host?

> **Answer:** DEV-QA-SERVER

***

## Task 4: Web Shell Alert

**Alert Details:**

* **Alert Name:** Potential Web Shell Upload Detected
* **Resource:** `http://web.trywinme.thm`
* **Suspicious IP:** `171.251.232.40`

Initial OSINT checks on AbuseIPDB confirm the source IP is highly malicious. The investigation pivots to web logs to trace the attacker's actions, uncovering a Hydra brute-force attack followed by interaction with a known PHP web shell (`b374k.php`).

**Splunk Investigation Queries:**

1.  **General Traffic Analysis:** Structure the web logs into a readable format for the suspicious IP.

    ```bash
    index=web-alert 171.251.232.40
    | table req_time, clientip, uri, uri_path, method, status, useragent
    ```
2.  **Filtering Brute Force Noise:** Look for post-exploitation activity by filtering out the massive volume of requests targeting the login page.

    ```bash
    index=web-alert 171.251.232.40 uri!="/wp-login.php"
    | table req_time, clientip, uri, uri_path, method, status, useragent
    ```
3.  **Isolating Hydra Activity:** Find the exact start time of the automated brute-force attack.

    ```bash
    index=web-alert 171.251.232.40 useragent="Mozilla/5.0 (Hydra)"
    | table req_time, clientip, uri, uri_path, method, status, useragent
    ```
4.  **Web Shell Interaction Count:** Find the specific number of requests made via the identified web shell (`b374k.php`) by checking the count of logs where the shell name appears in the URI or referer.

    ```bash
    index=web-alert b374k.php
    ```

Question: What time did the brute-force activity using Hydra begin? Answer Format Example: 2025-01-15 12:30:45

> **Answer:** 2025-09-14 21:20:27

Question: Which user agent did the attacker use when interacting with the web shell?

> **Answer:** Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36

Question: What was the number of requests made by the attacker to the server via the web shell?

> **Answer:** 4

***

## Conclusion

This room provided practical experience in triaging complex alerts spanning Linux brute-forcing, Windows persistence mechanisms, and web shell exploitation. By utilizing targeted Splunk queries, analysts can filter through the noise to construct a reliable timeline of the attacker's actions, enabling swift and confident incident escalation.
