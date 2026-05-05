---
icon: shield
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/616945d482ef350052080da1-1754137100965
coverY: 0
coverHeight: 138
---

# Detecting Web Attacks

**Date:** 27.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Identifying web-based threats through the analysis of HTTP access logs, network packet captures (PCAPs), and the implementation of WAF rules.

Web attacks are primary entry points for attackers. This document outlines the detection of common vulnerabilities such as XSS, SQLi, and Brute-force attacks using industry-standard analysis techniques.

***

## Task 1: Introduction

The objective is to understand client/server-side attacks and leverage logs, network traffic, and WAFs for mitigation.

Question: I understand the learning objectives and am ready to learn about detecting web attacks!

> **Answer:** No answer needed

***

## Task 2: Client-Side Attacks

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Client-side attacks exploit user behavior or device vulnerabilities (browsers/plugins).

* **Visibility Issue:** SOC analysts have limited visibility into browser-side execution without endpoint monitoring.

<figure><img src="../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Common Attacks:**
  * **XSS (Cross-Site Scripting):** Executing malicious scripts in a user's browser.
  * **CSRF (Cross-Site Request Forgery):** Forcing a user to send unauthorized requests.
  * **Clickjacking:** Overlaying invisible elements to trick user interaction.

Question: What class of attacks relies on exploiting the user's behavior or device?

> **Answer:** Client-Side

Question: What is the most common client-side attack?

> **Answer:** XSS

***

## Task 3: Server-Side Attacks

<figure><img src="../.gitbook/assets/image (11) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Server-side attacks target the web server, application code, or backend databases.

* **Common Attacks:**
  * **Brute-Force:** Repeated attempts to guess credentials.
  * **SQL Injection (SQLi):** Manipulating database queries to leak/modify data.
  * **Command Injection:** Executing OS commands via vulnerable input fields.

<figure><img src="../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: What class of attacks relies on exploiting vulnerabilities within web servers?

> **Answer:** Server-Side

Question: Which server-side attack lets attackers abuse forms to dump database contents?

> **Answer:** SQLi

***

## Task 4: Log-Based Detection

<figure><img src="../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>

Access logs provide a trail of evidence for every request.

* **Key Fields:** Client IP, Timestamp, Status Code, and User-Agent.
* **Attack Indicators:** \* `404 Not Found`: Frequent errors suggesting directory fuzzing.
  * `302 Found`: Often indicates a successful login redirect during a brute-force attack.
  * `403 Forbidden`: Access attempts to restricted administrative directories.

**Investigation (access.log):** The attacker used `ffuf` for directory discovery, found `login.php`, brute-forced it, and then used SQLi on `changeusername.php`.

Question: What is the attacker's User-Agent while performing the directory fuzz?

> **Answer:** FFUF v2.1.0

Question: What is the name of the page on which the attacker performs a brute-force attack?

> **Answer:** /login.php

Question: What is the complete, decoded SQLi payload the attacker uses on the /changeusername.php form?

> **Answer:** %' OR '1'='1

***

## Task 5: Network-Based Detection

Network traffic analysis (Wireshark) provides the verbosity missing in logs, including full POST bodies and HTTP headers.

<figure><img src="../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Wireshark Filter:** `http` or `http.request.method == "POST"`.
* **HTTP Stream:** Right-click -> Follow -> HTTP Stream to see the full clear-text conversation between the attacker and server.

<figure><img src="../.gitbook/assets/image (15) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Investigation (traffic.pcap):** By following the HTTP stream on the `POST` requests to `login.php`, we can identify the specific credentials used. For the SQLi attack, the network capture reveals the actual database contents returned to the attacker.

Question: What password does the attacker successfully identify in the brute-force attack?

> **Answer:** astrongpassword123

Question: What is the flag the attacker found in the database using SQLi?

> **Answer:** THM{dumped\_the\_db}

***

## Task 6: Web Application Firewall (WAF)

<figure><img src="../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

WAFs act as gatekeepers, inspecting and filtering requests before they reach the server.

* **Detection Methods:** Signature-based (known patterns like `sqlmap`), Heuristic (behavioral context), and IP Reputation.
* **Challenge Mechanisms:** Using CAPTCHAs to filter automated bot traffic (which accounts for \~37% of web traffic).

Question: What do WAFs inspect and filter?

> **Answer:** Web Requests

Question: Create a custom firewall rule to block any User-Agent that matches "BotTHM".

> **Answer:** IF User-Agent CONTAINS "BotTHM" THEN block

***

## Conclusion

Effective detection requires correlating multiple data sources: logs for high-level patterns, network traffic for deep payload analysis, and WAFs for real-time mitigation.
