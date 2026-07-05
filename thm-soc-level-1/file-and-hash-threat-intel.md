---
icon: inbox-full
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/5fc2847e1bbebc03aa89fbf2-1755611001873
coverY: 0
coverHeight: 139
---

# File and Hash Threat Intel

**Date:** 10.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Enriching file and hash artifacts using threat intelligence tools, analyzing heuristic indicators, and performing sandbox dynamic analysis.

Security Operations teams live inside alert queues, following three essential steps: verify, enrich, and decide. File and hash intelligence resides squarely in the enrich phase, transforming an isolated file path or hash value into contextual knowledge about malicious artifacts.

***

## Task 1: Introduction

This room teaches how to interpret suspicious file paths and filenames using heuristics, generate and validate file hashes, leverage threat intelligence platforms (VirusTotal, MalwareBazaar) to enrich observed binaries, and extract behavior from sandbox telemetry mapped to MITRE ATT\&CK.

**Scenario:** During a routine alert sweep, an EDR tool flags multiple binaries on various workstations. The objective is to analyze a curated triage package of these samples to provide evidence demonstrating whether these files are bait, benign, or malicious. Due to the changing nature of threat intelligence, an offline tool named _TryDetectThis_ is utilized to search for hashes, review vendor detections, inspect file properties, and analyze sandbox behavior.

Question: Dive into file threat intelligence.

> **Answer:** No answer needed

***

## Task 2: Filenames and Paths

<figure><img src="../.gitbook/assets/image (5) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Human-readable strings, such as file paths and names, are the earliest heuristics available. While they do not independently prove maliciousness, they reveal attacker tradecraft patterns.

**Filepath Analysis:** Attackers utilize different disk locations to hide actions and reduce visibility:

* `C:\` (System drive): Common target for persistence mechanisms.
* `C:\Users\Public`: Enables cross-user access of detonated adversary tools.
* `C:\Users\Public\Public Downloads`: High-traffic directory that evades strict monitoring.
* `C:\Windows\Temp\`: Used for staging ephemeral payloads.
* `C:\ProgramData\`: A writable system path often used for stealth persistence.

**Filename Heuristic Indicators:** Attackers modify filenames to escape detection via several techniques:

* **Double extensions:** Features like `invoice.pdf.exe` leverage Windows default settings that hide known file extensions.
* **System binary impersonation:** Naming a file `scvhost.exe` abuses user familiarity with core system processes. Defenders should allowlist legitimate locations for system processes rather than relying on standalone filenames.
* **High-entropy Strings:** Filenames like `jh8F21.exe` suggest automated packing or polymorphic generation.
* **Masquerading:** Filenames like `backup-2300.exe` blend with routine files. Single character substitutions can visually deceive unsuspecting employees.

Question: One of the files included in the CTI Files folder on the Desktop shows one of the indicators mentioned. Can you identify the file and the indicator? (Answer: file, property)

> **Answer:** payroll.pdf, Double extensions

***

## Task 3: File Hash Lookup

Because attackers constantly rename files, analyzing paths and names can be inconclusive. Cryptographic fingerprinting (SHA256 and MD5 hashes) uniquely identifies files and malware regardless of name changes.

**Generating Hashes:**

```bash
# Windows Command Prompt
certutil -hashfile bl0gger.exe SHA256
```

```bash
# Windows PowerShell
Get-Filehash -Algorithm SHA256 bl0gger.exe
```

```
# Linux
sha256sum bl0gger.exe
```

**Hash Analysis Pointers:**

* Store hashes in lowercase to avoid needless differences.
* Hash what matters (e.g., if malware is in a ZIP, hash both the archive and the extracted binary).
* Any byte change alters the resulting hash.

**Analysis With VirusTotal:** VirusTotal aggregates scan results from dozens of antivirus vendors. Key items to note:

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Detection score:** Crowdsourced security verdict (ratio).
* **Threat labels and categories:** Vendor-specific classifications.
* **Detection rules:** Technical signatures (YARA rules, Heuristic patterns).
* **Properties:** Core metadata (file type, size, compilation timestamp).
* **Contained domains and IPs:** Malware network infrastructure.

<table data-header-hidden><thead><tr><th width="191.4000244140625"></th><th width="214"></th><th></th></tr></thead><tbody><tr><td>Section</td><td>Key Question</td><td>Red Flags / Analyst Considerations</td></tr><tr><td><strong>Detection Score and Threat Labels</strong></td><td>How many vendors detect this file as malicious?</td><td>Five or more solid vendors flag it. Conflicting classifications (e.g., "Trojan" vs "PUA"). New malware often has low initial detection; recheck after 24h.</td></tr><tr><td><strong>Upload Time</strong></td><td>When was the file first submitted?</td><td>Sudden detection spike after days/weeks. Vendors need 48-72 hours for full analysis.</td></tr><tr><td><strong>Signatures</strong></td><td>Is the file properly signed?</td><td>Invalid/missing certificate or issued to an unrelated entity. Check cert chain expiration dates.</td></tr><tr><td><strong>Properties</strong></td><td>Are there anomalies in the file data?</td><td>Compile timestamp at odd hours (e.g., 3 AM). High entropy (>7.5) in non-media files. Compare with known-good versions.</td></tr><tr><td><strong>Relations</strong></td><td>What infrastructure does the malware connect to?</td><td>Known-bad IPs, DGA-like domains (e.g., <code>xk8f92.xyz</code>). Check IPs in Shodan for open ports.</td></tr><tr><td><strong>Behavioral</strong></td><td>What post-execution actions occur?</td><td>Modifies critical registry keys, attempts process injection. Correlate with endpoint logs.</td></tr></tbody></table>

**Cross-Reference With MalwareBazaar:** MalwareBazaar is a database for malware collection. Key features include:

* **Malware Family tagging:** Files with low VT detection but tagged (e.g., `#IcedID`) should be treated as malicious.
* **YARA rule integration:** Includes rules detecting related samples for future hunting.
* **Campaign attribution:** Tags like `#TA551` link incidents to known adversaries.
* **Search Syntax:** `sha256:<file_hash>`

Question: What is the SHA256 hash of the file bl0gger?

> **Answer:** 2672b6688d7b32a90f9153d2ff607d6801e6cbde61f509ed36d0450745998d58

Question: What is the threat classification label used to identify the malicious file?

> **Answer:** trojan.graftor/flystudio

Question: When was the file first submitted for analysis? (Answer format: YYYY-MM-DD HH:MM:SS)

> **Answer:** 2025-05-15 12:03:49

Question: Which vendor classified the Morse-Code-Analyzer file as non-malicious?

> **Answer:** CyberFortress

Question: What MITRE technique has been flagged for persistence and privilege escalation for the Morse-Code-Analyzer file?

> **Answer:** DLL Side-Loading

***

## Task 4: Sandbox Analysis

While static properties (hashes, strings) reveal identity, dynamic analysis in a sandbox reveals impact. Sandboxes are instrumented, disposable VMs that capture processes, registry writes, and network packets.

**Sandboxing Goals:**

* Confirm execution (identify decoys).
* Extract runtime IOCs (domains, mutexes, dropped payloads).
* Map to ATT\&CK technique IDs automatically.

**Sandboxing Tools:**

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Hybrid Analysis (HA):** Focuses on behavior trees and a clean MITRE ATT\&CK heatmap. Ideal for fast executive summaries.
* **Joe Sandbox (JS):** Covers deep system calls, strings, and memory dumps. Ideal for reverse/detection engineers.

**Sandboxing Limitations:** Relying solely on sandboxes can lead to false negatives due to:

* **Sandbox Evasion Techniques:** Malware checks for virtualized environments, performs debugger detection, or checks for unique hardware IDs.
* **Limited Execution Time & Coverage:** Sandboxes terminate analysis after 2-5 minutes, missing multi-stage or time-delayed attacks.
* **Encrypted & Obfuscated Traffic:** Sandboxes may fail to decrypt SSL/TLS traffic or miss data hidden via DNS tunneling.
* **Fileless & Living-off-the-Land (LotL) Malware:** Threats utilizing PowerShell or WMI persistence may bypass traditional sandbox disk analysis.

Question: What tags are used to identify the bl0gger.exe malicious file on Hybrid Analysis? (Answer: Tag1, Tag2, Tag3)

> **Answer:** BlackMoon, Discovery, windows-server-utility

Question: What was the stealth command line executed from the file?

> **Answer:** regsvr32 %WINDIR%\Media\ActiveX.ocx /s

Question: Which other process was spawned according to the process tree?

> **Answer:** werfault.exe

Question: Analyze the payroll.pdf file located in the CTI Files folder and answer the questions below. The payroll.pdf application seems to be masquerading as which known Windows file?

> **Answer:** svchost.dll

Question: What associated URL is linked to the file?

> **Answer:** hxxp://121.182.174.27:3000/server.exe

Question: How many extracted strings were identified from the sandbox analysis of the file?

> **Answer:** 454

***

## Task 5: Threat Intelligence Challenge

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

This task requires applying the concepts to investigate a suspected file named `Challenge.bin.sample` using the offline TryDetectThis threat intelligence tool to extract its attributes, execution parameters, and MITRE mapping.

Question: What is the SHA256 hash of the file?

> **Answer:** 43b0ac119ff957bb209d86ec206ea1ec3c51dd87bebf7b4a649c7e6c7f3756e7

Question: What family labels are assigned to the file on VirusTotal?

> **Answer:** akira, filecryptor

Question: When was the first time the file was recorded in the wild? (Answer Format: YYYY-MM-DD HH:MM:SS UTC)

> **Answer:** 2024-10-30 17:17:24 UTC

Question: Name the text file dropped during the execution of the malicious file.

> **Answer:** akira\_readme.txt

Question: What PowerShell command is observed to be executed?

> **Answer:** Get-WmiObject Win32\_Shadowcopy | Remove-WmiObject

Question: What MITRE ATT\&CK ID is associated with the actions of the command?

> **Answer:** T1490

***

## Conclusion

Threat intelligence is integral to the SOC workflow, enriching unknown binaries encountered during alert triage.

**Key Takeaways:**

* Validate evidence before analysis.
* Analyze file paths and filenames for unusual storage locations and naming tricks.
* Generate hashes early to pivot into external knowledge bases (VirusTotal, MalwareBazaar).
* Observe runtime behavior in controlled environments (Hybrid Analysis) while remaining aware of sandbox evasion tactics.
* Translate findings into actionable briefs containing IOCs and behavioral summaries.
