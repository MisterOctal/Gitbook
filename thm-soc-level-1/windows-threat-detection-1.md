---
icon: windows
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1748645181309
coverY: 0
coverHeight: 138
---

# Windows Threat Detection 1

**Date:** 30.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Exploring common Initial Access methods on Windows environments and learning how to detect them using Windows event logs and Sysmon.

The moment a threat actor successfully breaches a target system is known as Initial Access. This document explores the methodology behind exposed service exploitation and user-driven compromises, providing the necessary steps to detect these events using native Windows logging mechanisms.

***

## Task 1: Introduction

The objective is to explore how threat actors access and breach Windows machines. This includes learning common Initial Access techniques via real-world examples and practicing detection using Windows event logs.

Question: Let's begin!

> **Answer:** No answer needed

***

## Task 2: Intro to Initial Access

Initial Access represents the first step of a breach. Methods are generally divided into two groups: those requiring an exposed service and those relying on human interaction.

**Exposed Services** Placing a Windows server directly on the Internet introduces significant security risks. Automated bots actively scan for open ports, weak passwords, and unpatched vulnerabilities.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **T1133 / External Remote Services:** Threat actors look for exposed RDP/VNC/SSH with weak passwords to gain remote access.
* **T1190 / Exploit Public-Facing Application:** Threat actors target misconfigured or vulnerable websites and applications.

**User-Driven Methods** If a machine is not exposed to the Internet, infection typically requires human interaction, such as clicking malicious links, opening phishing attachments, or plugging in unknown USB devices.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **T1566 / Phishing:** Threat actors employ various phishing techniques to trick victims into launching malware.
* **T1091 / Removable Media:** Threat actors infect a USB device, relying on victims to plug the hardware into multiple workstations.

Question: Which MITRE technique ID describes Initial Access via a vulnerable mail server?

> **Answer:** T1190

Question: Which Initial Access method relies on a user opening a malicious email attachment?

> **Answer:** Phishing

***

## Task 3: Initial Access via RDP

Exposing Remote Desktop Protocol (RDP) with weak credentials often results in rapid compromise. RDP is frequently referred to as the "Ransomware Deployment Protocol" due to its high correlation with ransomware attacks. Exposed RDP services generate hundreds of 4625 events as botnets actively brute-force accounts.

**Detecting an RDP Breach** An RDP breach can be reconstructed entirely through Security logs:

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

1. **RDP Brute Force:** Filter Security logs for failed logins (Event ID 4625), logon types 3 and 10 (remote logons), and external source IPs.
2. **Initial Access:** Switch the filter to successful logins (Event ID 4624) and identify the compromised account.
3. **Further Actions:** Filter for interactive RDP logins (Logon Type 10), copy the Logon ID field, and search Sysmon logs for the same Logon ID to identify all processes executed by the threat actor during the session.

Question: Which user seems to be most actively brute-forced by botnets?

> **Answer:** Administrator

Question: Which IP managed to breach the host via RDP (Logon Type 10)?

> **Answer:** 203.205.34.107

Question: Can you get the real Workstation Name (hostname) of the threat actor?

> **Answer:** DESKTOP-QNBC4UU

***

## Task 4: Initial Access via Phishing

Phishing remains a primary threat vector due to its high success rate in bypassing perimeter firewalls. Attackers leverage multiple attachment types to deceive victims.

**Binary Attachments** Threat actors abuse executable extensions (.exe, .com, .scr, .cpl). Because Windows hides known file extensions by default, a file explicitly named "invoice.pdf.exe" appears simply as "invoice.pdf" in File Explorer, tricking the recipient.

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1).png" alt="" width="501"><figcaption></figcaption></figure>

**LNK Attachments** To evade antivirus detection, attackers hide scripts (PowerShell, Visual Basic, BAT) within LNK shortcut files. The LNK "Target" field contains the malicious command. For example, a shortcut masquerading as a website link may point to a hidden PowerShell command that downloads and executes malware like RemcosRAT.

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: Let's play the role of the untrained user and mindlessly open the COM file. Run the www.skype.com file from the Phishing Case 1 folder, which flag do you get?

> **Answer:** THM{misleading\_extension}

Question: Continue with the second attachment from the Phishing Case 2 folder. From which URL does the malicious LNK download the next stage malware?

> **Answer:** http://wp16.hqywlqpa.thm:8000/cgi-bin/f

Question: Finally, move on to the Phishing Case 3 folder and review its content. What is the name of the double-extension file you see there?

> **Answer:** best-cat.jpg.exe

***

## Task 5: Continuing Phishing Topic

Hunting for malicious downloads requires tracking the file's lifecycle from the browser to execution.

**Detecting Malicious Downloads with Sysmon:**

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Sysmon Event ID 1:** Web browser is launched (e.g., msedge.exe).
* **Sysmon Event ID 11:** An archive file appears in the Downloads directory (.zip).
* **Sysmon Event ID 11:** The archive is extracted (e.g., invoice.pdf.exe).
* **Sysmon Event ID 1:** The unarchived malicious file is double-clicked and launched.

**Notes on LNK Attachments:** LNK files leave minimal execution traces. When launched, Windows Explorer reads the Target field, making it appear as though `explorer.exe` launched the payload (e.g., PowerShell) directly. Identifying LNK phishing requires looking for preceding file creation events (Event ID 11) to confirm the LNK file appeared in the file system before execution.

<figure><img src="../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: Which file did the user download via the web browser?

> **Answer:** C:\Users\Administrator\Downloads\top-cats.zip

Question: In which folder did the user unarchive the suspicious file?

> **Answer:** C:\Users\Administrator\Pictures

Question: What is the process ID of the launched phishing malware?

> **Answer:** 5484

Question: Finally, which malicious domain did the malware try to connect to?

> **Answer:** rjj.store

***

## Task 6: Initial Access via USB

Initial Access via an infected USB bypasses network firewalls and can start the attack chain without Internet access. Common scenarios include targeted delivery of infected drives via mail or accidental infections from third-party services like print shops.

**Detecting an Infected USB:** Detection is conceptually similar to identifying phishing attachments, as both rely on executing binaries via the graphical interface (`explorer.exe`). Key indicators include process creation events where the image path originates from an unusual drive letter (e.g., `E:\malware.exe`), providing clear evidence that the execution stemmed from a removable drive.

<figure><img src="../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

Question: Which USB file was launched by the user?

> **Answer:** E:\Open Sandisk 4GB USB.exe

Question: Which suspicious file did the malware drop to the disk? (Format: full path to the file, e.g. C:\file.txt)

> **Answer:** C:\Users\Public\Documents\winupdate.exe

Question: To which other USB did the malware propagate? (Format: just the letter, e.g. X:)

> **Answer:** F:

***

## Conclusion

Understanding Initial Access methods is critical for quick alert triage and incident response. Exposed services are highly visible in authentication logs (4624/4625), while user-driven attacks (Phishing, USB) require thorough process execution monitoring via Sysmon to uncover the complete attack lifecycle.
