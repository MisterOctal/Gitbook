---
icon: shield
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1754048680674
coverY: 0
coverHeight: 138
---

# Detecting Web Shells

**Date:** 27.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Identifying web shells by analyzing web server access logs, Linux audit logs (auditd), file system changes, and network traffic patterns.

Web shells are malicious scripts uploaded to a compromised web server to provide attackers with persistent remote access and command execution capabilities. This room explores the lifecycle of a web shell from deployment to detection.

***

## Task 1: Introduction

Knowing how to detect web shells is an essential skill for SOC Analysts and Incident Responders. Web shells are a common technique attackers use to gain an initial foothold, providing remote access that enables various actions later in the attack chain.

Question: I understand the learning objectives and am ready to embark on a web shell adventure.

> **Answer:** No answer needed

***

## Task 2: Web Shell Overview

A web shell is a malicious program uploaded to a target web server, enabling adversaries to execute commands remotely. They serve as both an initial access method and a persistence mechanism.

<figure><img src="../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

#### Key Deployment Concepts

* **Vulnerabilities:** Exploited via file upload vulnerabilities, misconfigurations, or prior system access.
* **Kill Chain:** Used for reconnaissance, privilege escalation, lateral movement, and data exfiltration.
* **Real-World Examples:**
  * **Hafnium (ProxyLogon):** Used `.aspx` shells on Microsoft Exchange servers.
  * **Conti Ransomware:** Deployed backup web shells within minutes of initial access to map out domain controllers.

Question: Which MITRE ATT\&CK Persistence sub-technique are web shells associated with?

> **Answer:** T1505.003

Question: What file extension is commonly used for web shells targeting Microsoft Exchange?

> **Answer:** .aspx

***

## Task 3: Anatomy of a Web Shell

Web shells abuse legitimate system execution functions. In PHP, common abusable functions include:

* `shell_exec()`
* `exec()`
* `system()`
* `passthru()`

**Simple PHP Web Shell Example:**

<figure><img src="../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

Question: Access the shell and determine which account you have access to by running the `whoami` command.

> **Answer:** www-data

Question: List the directory contents and find the flag using the `ls` and `cat` commands.

> **Answer:** THM{W3b\_Sh3ll\_Usag3}

***

## Task 4: Log-Based Detection

#### Web Server Logs

<figure><img src="../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

Access logs are the first place to hunt. Key indicators include:

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

* **Unusual HTTP Methods:** High volume of `POST` requests to static directories.
* **Status Codes:** Success codes (`200 OK`) on files that shouldn't exist.
* **Suspicious User-Agents:** Tools like `curl`, `wget`, or malformed strings.
* **Query Strings:** Parameters like `cmd=`, `exec=`, or Base64 encoded values.

#### Auditd (Linux Auditing)

`auditd` tracks file creations (`creat`) and command executions (`execve`).

* **Command:** `ausearch -k web_shell` searches for events tagged with a specific rule.

Question: What is the part of the URL that associates values to parameters and can be a valuable indicator of web shell activity?

> **Answer:** query strings

Question: What auditd syscall would confirm that a file was written to disk following a suspicious POST request to /upload.php?

> **Answer:** creat

***

## Task 5: Beyond Logs

#### File System Analysis

Web shells are often hidden in common paths like `/var/www/html/uploads/`, `/var/www/html/images/`, or `/tmp/`.

**Detection Commands:**

* **Find modified files:** `find /var/www -type f -name "*.php" -newerct "2025-07-01" ! -newerct "2025-08-01"`
* **Search for suspicious functions:** `grep -r "eval(" wp-content`

#### Network Traffic Analysis

Using Wireshark, look for:

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

* `http.request.method == "POST"`: To find the initial upload.
* `http.request.uri contains ".php"`: To find the script being called.
* **Payload Inspection:** Look for `<?php` or command strings in the packet bytes.

Question: What command would you use to locate .php files in the /var/www/ directory?

> **Answer:** find /var/www/ -type f -name "\*.php"

Question: Which Wireshark filter would you use to search specifically for PUT requests?

> **Answer:** http.request.method == "PUT"

***

## Task 6: Investigation

In this scenario, we investigate a compromised WordPress site by analyzing `/var/log/apache2/access.log`.

#### Command Reference for Investigation

* **Filter for 404 errors:** `cat /var/log/apache2/access.log | grep "404"`
* **Filter for specific IP:** `grep "203.0.113.66" /var/log/apache2/access.log`
* **Count unique User-Agents:** `cat access.log | cut -d'"' -f6 | sort | uniq -c`

Question: Which IP address likely belongs to the attacker?

> **Answer:** 203.0.113.66

Question: What is the first directory that the attacker successfully identifies?

> **Answer:** /wordpress

Question: What is the name of the .php file the attacker uses to upload the web shell?

> **Answer:** upload\_form.php

Question: What is the first command run by the attacker using the newly uploaded web shell?

> **Answer:** whoami

Question: After gaining access via the web shell, the attacker uses a command to download a second file onto the server. What is the name of this file?

> **Answer:** linpeas.sh

Question: The attacker has hidden a secret within the web shell. Use cat to investigate the web shell code and find the flag.

> **Answer:** THM{W3b\_Sh3ll\_Int3rnals}

***

## Conclusion

Detection is most effective when correlating web logs (who accessed it), file system changes (what was created), and process monitoring (what the shell executed).
