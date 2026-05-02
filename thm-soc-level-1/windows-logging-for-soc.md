---
icon: windows
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1748608721198
coverY: 0
coverHeight: 139
---

# Windows Logging for SOC

**Date:** 29.04.2026&#x20;

**Room Category:** Research & Analysis&#x20;

**Core Concept:** Understanding and interpreting Windows event logs, utilizing Sysmon for advanced process monitoring, and analyzing PowerShell history for threat detection.

SOC analysts frequently rely on system logs within a SIEM to triage alerts and hunt threats. Distinguishing benign from malicious activity requires an in-depth understanding of how Windows records events, the structure of specific event IDs, and the correlation between authentication, user management, and process execution logs.

***

## Task 1: Introduction

Proper log analysis is a fundamental skill for SOC and DFIR professionals. The objective is to navigate Windows logging mechanisms to reconstruct attack chains and detect malicious activity accurately.

**Learning Objectives:**

* Understand how to find and interpret important Windows event logs.
* Utilize advanced monitoring log sources like Sysmon and PowerShell.
* Prepare for using these logs in SIEM environments.
* Practice log analysis skills on multiple event log datasets.

Question: I'm ready to move on!

> **Answer:** No answer needed

***

## Task 2: What Is Logged

Whenever an application is launched, a file is created, or a user authenticates, the Operating System processes and logs the event. These logs state the time, action details, and the user behind the action, which assists in Incident Response, Threat Hunting, and Alert Triage.

<figure><img src="../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

**Anatomy of a Log Entry** Windows stores logs in a binary format inside the `C:\Windows\System32\winevt\Logs` directory. Every `.evtx` file corresponds to a specific log category:

* **Application Logs:** Events from user-mode applications (IIS web server, MS SQL).
* **Security Logs:** Events concerning authentication, process activity, and user management.

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

**Reading Event Logs** The built-in Windows Event Viewer (`eventvwr`) parses these binary files into a readable format. Key components of the interface include:

* **Log Sources:** The specific `.evtx` category.
* **Log List:** A row-by-row view of events, sortable by Keywords, Date and Time, and Event ID.
  * **Event ID:** A unique numerical identifier for the event type (e.g., 4625 for a failed login).
* **Log Details:** The plaintext or XML content containing the exact event parameters.
* **Filters Menu:** Used to filter logs and find other correlated logs.

Question: Looking at the last screenshot, which event ID describes a successful login?

> **Answer:** Security / 4624

***

## Task 3: Security Log: Authentication

The Security event log provides critical visibility into authentication attempts. The two most prominent event IDs are 4624 (Successful Logon) and 4625 (Failed Logon).

* **Event ID 4624 (Successful Logon):** Detects RDP and network logins. It is logged on the target machine. This event can be noisy, as heavily loaded servers generate hundreds of logons per minute.
* **Event ID 4625 (Failed Logon):** Detects brute force, password spraying, or vulnerability scanning. Also logged on the target machine.

**Structure of 4624/4625 Events** Key fields to review include:

<figure><img src="../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

* Logon ID (A unique session identifier)
* Logon Type
* Username
* Source IP and Hostname

**RDP Brute Force Detection Methodology**

1. Filter for Event ID 4625.
2. Isolate Logon Type 3 (Network) and Logon Type 10 (RDP). Modern systems with Network Level Authentication (NLA) default to Type 3 for RDP failures.
3. Identify red flags:
   * Password spraying indicators (multiple accounts attempted, e.g., admin, helpdesk).
   * Brute force indicators (many failures on a single account, typically Administrator).
   * Anomalous Workstation Names that do not match corporate naming conventions.
   * Unexpected Source IPs.

**RDP Logon Analysis Methodology**

1. Filter for Event ID 4624.
2. Isolate Logon Type 10 (RDP).
3. If NLA is enabled, every Type 10 RDP logon is preceded by a Type 3 logon. The true Workstation Name is often found in the preceding Type 3 event.
4. Record the assigned Logon ID (e.g., `0x5D6AC`) to correlate future activities back to this specific session.

Question: Which IP performed a brute force of the THM-PC?

> **Answer:** 10.10.53.248

Question: Which user has been breached as a result of the attack?

> **Answer:** Administrator

Question: What was the Logon ID of the malicious RDP login?

> **Answer:** 0x183C36D

***

## Task 4: Security Log: User Management

Monitoring user management events is crucial for identifying persistence mechanisms, such as the creation of backdoor accounts or unauthorized privilege escalation.

**Common User Management Event IDs:**

* **4720 / 4722 / 4738:** User account created, enabled, or changed. Attackers create backdoor accounts or re-enable old ones.
* **4725 / 4726:** User account disabled or deleted. Advanced threat actors may disable SOC accounts to delay response.
* **4723 / 4724:** Password changed or reset. Attackers with sufficient privileges may reset a target's password for access.
* **4732 / 4733:** User added to or removed from a security group. Backdoor accounts are frequently added to the "Administrators" group.

**Structure of User Management Events** These events are split into three logical parts:

<figure><img src="../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

* **Subject:** The account performing the action. The Logon ID field here can be correlated with the preceding 4624 login event.
* **Object:** The target of the action (the affected account).
* **Details:** Group names or new attributes (full name, password settings).

**Hunting for Backdoored Users Methodology**

1. Filter for 4720 and 4732 event IDs.
2. Review events for anomalies: modifications during non-working hours, actions performed by unexpected subject accounts, or target usernames that violate corporate naming standards.
3. Correlate the Logon ID of the 4720/4732 event back to the corresponding 4624 login event to identify the attacker's origin.

Question: Which user was created by the attacker soon after the RDP login?

> **Answer:** svc\_sysrestore

Question: Which two privileged groups was the backdoor user added to?

> **Answer:** Backup Operators, Remote Desktop Users

Question: Does the Logon ID field match what you saw in the previous task (Yea/Nay)?

> **Answer:** Yea

***

## Task 5: Sysmon: Process Monitoring

While authentication logs indicate who accessed a system, process monitoring reveals what actions were taken.

**Process Creation Event Alternatives:**

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

* **Security Log 4688:** Logs new process launches and command lines. Disabled by default and requires registry/GPO configuration.
* **Sysmon Event 1:** Replaces 4688 and provides advanced fields like process hashes and signatures. Installed via Microsoft Sysinternals.

Sysmon logs are located under `Applications & Services -> Microsoft -> Windows -> Sysmon -> Operational`.

**Structure of Sysmon Event ID 1**

* **Process Info:** PID, path (image), and full command line.
* **Parent Info:** Context of the parent process, vital for building an attack chain.
* **Binary Info:** Process hash (MD5/SHA256) and PE metadata.
* **User Context:** Executing user and Logon ID.

**Process Launch Analysis Methodology**

1. Filter for Sysmon Event ID 1.
2. Review Process and Binary info for red flags: executables in staging directories (`C:\Temp`, `C:\Users\Public`), randomized file names, or known malicious hashes (via VirusTotal).
3. Review Parent Info: ensure the parent process matches expected behavior (e.g., Notepad spawning a command shell is highly anomalous).
4. Traverse the process tree by matching `ProcessId` to `ParentProcessId` in preceding events.
5. Correlate with Security events using the Logon ID.

Question: Which web browser does Sarah use to browse the web?

> **Answer:** Google Chrome

Question: Which file did Sarah download from the browser?

> **Answer:** C:\Users\sarah.miller\Downloads\ckjg.exe

Question: Which URL was the file downloaded from?

> **Answer:** http://gettsveriff.com/bgj3/ckjg.exe

***

## Task 6: Sysmon: Files and Network

Sysmon extends visibility beyond process creation to include file modifications, registry changes, and network activity.

**Additional Sysmon Event IDs:**

* **Event ID 11 (File Create):** Detects dropped malware payloads or scripts. (Security Log alternative: 4656).
* **Event ID 13 (Registry Value Set):** Detects registry modifications used for persistence. (Security Log alternative: 4657).
* **Event ID 3 (Network Connection):** Detects outbound traffic to C2 servers.
* **Event ID 22 (DNS Query):** Detects queries to malicious or randomized domains.

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

**Structure and Correlation** These events often lack full user context or parent process information. Correlation is achieved by extracting the `ProcessId` from Event 11, 13, 3, or 22, and matching it against an Event ID 1 (Process Creation) to determine the responsible executable.

**Process Activity Analysis Methodology**

1. Extract `ProcessId` from suspicious Sysmon events.
2. Hunt for associated network activity: connections on non-standard ports (e.g., 4444), connections to malicious IPs, or queries to suspicious TLDs (`.top`, `.click`).
3. Hunt for file/registry activity: files dropped into `C:\Temp` or `C:\Users\Public`, or `.bat`/`.ps1`/`.exe` files configured for startup persistence.

Question: Which file was created by the downloaded malware to persist on the host?

> **Answer:** C:\Users\sarah.miller\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\DeleteApp.url

Question: What is the Command & Control server malware connected to?

> **Answer:** 193.46.217.4:7777

Question: Finally, which domain does the malicious IP correspond to?

> **Answer:** hkfasfsafg.click

***

## Task 7: PowerShell: Logging Commands

PowerShell is heavily abused for system discovery, payload downloading, and process injection. Process monitoring (Sysmon Event 1) only records the initial launch of `powershell.exe`, failing to capture the subsequent commands executed within that terminal session.

**The PowerShell History File** While mechanisms like AMSI and Script Block Logging exist, a highly effective and simple tracking method is the PowerShell history file. Path: `C:\Users\<USER>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

**Key Characteristics of the History File:**

* It is a plain text file automatically created and updated by PowerShell upon pressing Enter.
* It is extremely useful for tracking interactive malicious actions.
* It is generated on a per-user basis (multiple users mean multiple history files).
* It survives system reboots and retains historical commands permanently unless manually deleted.
* It logs entered commands but does not log command output or the internal contents of executed scripts (e.g., running `.\script.ps1` only logs the invocation, not the script's code).

Question: Review the Administrator's PS history on the attached VM. Which PowerShell command was executed first?

> **Answer:** Get-ComputerInfo

Question: When did the Administrator run the first PS command? (Format: April 18, 2025)

> **Answer:** May 18, 2025

Question: Can you find the flag stored in the PowerShell history? (Format: THM{...})

> **Answer:** THM{it\_was\_me!}

***

## Conclusion

Mastering Windows Event Logs and Sysmon is critical for tracing attacks across the Cyber Kill Chain.

**Key Takeaways:**

* Event IDs 4624 and 4625 are foundational for tracking authentication and initial access.
* Grouping logs by Logon ID and Process ID is the primary method for reconstructing attack chains.
* Sysmon provides superior context for process creations, network connections, and file modifications compared to default Windows logging.
* PowerShell history files provide critical visibility into commands executed within a single process session.
