---
icon: windows
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1729043928499.png
coverY: 0
coverHeight: 138
---

# CAPA: The Basics

**Date:** 03.04.2026

**Room Category:** Walkthrough

**Core Concept:** Learning to use CAPA (Common Analysis Platform for Artifacts) to perform static analysis and identify malicious capabilities in executable files.

Analyzing potentially malicious software presents a significant challenge: running the malware risks compromising the host machine unless a secure, isolated sandbox is used. To safely understand a binary without executing it, analysts rely on static analysis. This room focuses on leveraging CAPA, an automated static analysis tool, to extract and map malware capabilities efficiently.

***

## Task 1: Introduction

CAPA (Common Analysis Platform for Artifacts) is a powerful tool developed by the FireEye Mandiant team. It is designed to identify the capabilities present in executable files, such as Portable Executables (PE), ELF binaries, .NET modules, and shellcode. By analyzing the file against a vast set of rules describing common behaviors, CAPA can determine if a program is capable of network communication, file manipulation, process injection, and more.

<figure><img src="../.gitbook/assets/image (28) (1).png" alt="" width="352"><figcaption></figcaption></figure>

The true beauty of CAPA is that it encapsulates years of reverse engineering knowledge into an automated format. This makes it highly accessible for security professionals, allowing them to quickly understand a binary's functionality for incident response without having to manually reverse engineer the underlying assembly code.

Question: I'm excited to learn more about CAPA!

> **Answer:** No answer needed

***

## Task 2: Tool Overview: How CAPA Works

Running CAPA is incredibly straightforward via the command line. By supplying the target executable to `capa.exe`, the tool processes the file against its rule sets and outputs a comprehensive table of findings. Because deep analysis can take several minutes, the tool offers various parameters to control the depth of the output.

The `-v` (verbose) and `-vv` (very verbose) flags provide deeper insights but significantly increase processing time. In a Windows PowerShell environment, you can also use the `Get-Content` command to quickly read pre-processed text reports.

Below is an example of a CAPA output:

```bash
PS C:\Users\Administrator\Desktop\capa> capa .\cryptbot.bin
┌─────────────┬────────────────────────────────────────────────────────────────────────────────────┐
│ md5         │ 3b9d26d2e7433749f2c32edb13a2b0a2                                                   │
│ sha1        │ 969437df8f4ad08542ce8fc9831fc49a7765b7c5                                           │
│ sha256      │ ae7bc6b6f6ecb206a7b957e4bb86e0d11845c5b2d9f7a00a482bef63b567ce4c                   │
│ analysis    │ static                                                                             │
│ os          │ windows                                                                            │
│ format      │ pe                                                                                 │
│ arch        │ i386                                                                               │
│ path        │ /home/strategos/Room-CAPA/cryptbot.bin                                             │
└─────────────┴────────────────────────────────────────────────────────────────────────────────────┘
┌──────────────────────┬───────────────────────────────────────────────────────────────────────────┐
│ ATT&CK Tactic        │ ATT&CK Technique                                                          │
├──────────────────────┼───────────────────────────────────────────────────────────────────────────┤
│ DEFENSE EVASION      │ Obfuscated Files or Information [T1027]                                   │
│                      │ Obfuscated Files or Information::Indicator Removal from Tools [T1027.005] │
│                      │ Virtualization/Sandbox Evasion::System Checks [T1497.001]                 │
│ DISCOVERY            │ File and Directory Discovery [T1083]                                      │
│ EXECUTION            │ Command and Scripting Interpreter::PowerShell [T1059.001]                 │
│                      │ Shared Modules [T1129]                                                    │
│ IMPACT               │ Resource Hijacking [T1496]                                                │
│ PERSISTENCE          │ Scheduled Task/Job::At [T1053.002]                                        │
│                      │ Scheduled Task/Job::Scheduled Task [T1053.005]                            │
└──────────────────────┴───────────────────────────────────────────────────────────────────────────┘
┌─────────────────────────────┬────────────────────────────────────────────────────────────────────┐
│ MAEC Category               │ MAEC Value                                                         │
├─────────────────────────────┼────────────────────────────────────────────────────────────────────┤
│ malware-category            │ launcher                                                           │
└─────────────────────────────┴────────────────────────────────────────────────────────────────────┘
┌──────────────────────────┬──────────────────────────────────────────────────────────────────────────┐
│ MBC Objective            │ MBC Behavior                                                             │
├──────────────────────────┼──────────────────────────────────────────────────────────────────────────┤
│ ANTI-BEHAVIORAL ANALYSIS │ Virtual Machine Detection [B0009]                                        │
│ ANTI-STATIC ANALYSIS     │ Executable Code Obfuscation::Argument Obfuscation [B0032.020]            │
│                          │ Executable Code Obfuscation::Stack Strings [B0032.017]                   │
│ COMMUNICATION            │ HTTP Communication [C0002]                                               │
│                          │ HTTP Communication::Read Header [C0002.014]                              │
│ DATA                     │ Check String [C0019]                                                     │
│                          │ Encode Data::Base64 [C0026.001]                                          │
│                          │ Encode Data::XOR [C0026.002]                                             │
│ DEFENSE EVASION          │ Obfuscated Files or Information::Encoding-Standard Algorithm [E1027.m02] │
│ DISCOVERY                │ File and Directory Discovery [E1083]                                     │
│ EXECUTION                │ Command and Scripting Interpreter [E1059]                                │
│ FILE SYSTEM              │ Create Directory [C0046]                                                 │
│                          │ Delete File [C0047]                                                      │
│                          │ Read File [C0051]                                                        │
│                          │ Writes File [C0052]                                                      │
│ MEMORY                   │ Allocate Memory [C0007]                                                  │
│ PROCESS                  │ Create Process [C0017]                                                   │
└──────────────────────────┴──────────────────────────────────────────────────────────────────────────┘
┌──────────────────────────────────────────────────────┬──────────────────────────────────────────────┐
│ Capability                                           │ Namespace                                    │
├──────────────────────────────────────────────────────┼──────────────────────────────────────────────┤
│ reference anti-VM strings                            │ anti-analysis/anti-vm/vm-detection           │
│ reference anti-VM strings targeting VMWare           │ anti-analysis/anti-vm/vm-detection           │
│ reference anti-VM strings targeting VirtualBox       │ anti-analysis/anti-vm/vm-detection           │
│ contain obfuscated stackstrings (2 matches)          │ anti-analysis/obfuscation/string/stackstring │
│ reference HTTP User-Agent string                     │ communication/http                           │
│ check HTTP status code                               │ communication/http/client                    │
│ reference Base64 string                              │ data-manipulation/encoding/base64            │
│ encode data using XOR                                │ data-manipulation/encoding/xor               │
│ contain a thread local storage (.tls) section        │ executable/pe/section/tls                    │
│ get common file path                                 │ host-interaction/file-system                 │
│ create directory                                     │ host-interaction/file-system/create          │
│ delete file                                          │ host-interaction/file-system/delete          │
│ read file on Windows (4 matches)                     │ host-interaction/file-system/read            │
│ write file on Windows (5 matches)                    │ host-interaction/file-system/write           │
│ get thread local storage value                       │ host-interaction/process                     │
│ create process on Windows                            │ host-interaction/process/create              │
│ allocate or change RWX memory                        │ host-interaction/process/inject              │
│ reference cryptocurrency strings                     │ impact/cryptocurrency                        │
│ link function at runtime on Windows (5 matches)      │ linking/runtime-linking                      │
│ parse PE header (4 matches)                          │ load-code/pe                                 │
│ resolve function by parsing PE exports (186 matches) │ load-code/pe                                 │
│ run PowerShell expression                            │ load-code/powershell/                        │
│ schedule task via at                                 │ persistence/scheduled-tasks                  │
│ schedule task via schtasks                           │ persistence/scheduled-tasks                  │
└──────────────────────────────────────────────────────┴──────────────────────────────────────────────┘
```

Question: What command-line option would you use if you need to check what other parameters you can use with the tool? Use the shortest format.

> **Answer:** -h

Question: What command-line options are used to find detailed information on the malware's capabilities? Use the shortest format.

> **Answer:** -v

Question: What command-line options do you use to find very verbose information about the malware's capabilities? Use the shortest format.

> **Answer:** -vv

Question: What PowerShell command will you use to read the content of a file?

> **Answer:** Get-Content

***

## Task 3: Dissecting CAPA Results Part 1: General Information, MITRE and MAEC

When CAPA finishes its analysis, the first block of the report contains general information, including the file hashes (MD5, SHA1, SHA256), the analysis type, and the architecture. The tool then maps its findings to two major frameworks:

* **MITRE ATT\&CK:** This serves as a strategic playbook for defenders. CAPA maps the discovered capabilities directly to specific ATT\&CK Tactics (like Defense Evasion) and Techniques (like Obfuscated Files or Information).
* **MAEC (Malware Attribute Enumeration and Characterization):** This is a specialized language designed to encode details about malware. Common MAEC tags assigned by CAPA include "launcher" (triggering specific malicious actions or dropping payloads) and "Downloader" (fetching additional payloads from the internet).

Question: What is the sha256 of cryptbot.bin?

> **Answer:** ae7bc6b6f6ecb206a7b957e4bb86e0d11845c5b2d9f7a00a482bef63b567ce4c

Question: What is the Technique Identifier of Obfuscated Files or Information?

> **Answer:** T1027

Question: What is the Sub-Technique Identifier of Obfuscated Files or Information::Indicator Removal from Tools?

> **Answer:** T1027.005

Question: When CAPA tags a file with this MAEC value, it indicates that it demonstrates behaviour similar to, but not limited to, Activating persistence mechanisms?

> **Answer:** launcher

Question: When CAPA tags a file with this MAEC value, it indicates that the file demonstrates behaviour similar to, but not limited to, Fetching additional payloads or resources from the internet?

> **Answer:** Downloader

***

## Task 4: Dissecting CAPA Results Part 2: Malware Behavior Catalogue

The Malware Behavior Catalogue (MBC) serves as an extensive index of malware objectives and behaviors. It complements the MITRE framework by logging specific code features and low-level activities discovered during analysis.

The results are divided into several components:

* **Objective:** Broad goals like Anti-Behavioral Analysis, Credential Access, or Execution.
* **Micro-Objective:** Lower-level targets, such as PROCESS or MEMORY manipulation.
* **MBC Behaviors & Micro-Behaviors:** Specific actions like "Virtual Machine Detection" or "Create Process".
* **Methods:** Detailed sub-techniques explaining exactly how the behavior is achieved (e.g., Argument Obfuscation or Stack Strings).

Question: What serves as a catalogue of malware objectives and behaviours?

> **Answer:** Malware Behavior Catalogue

Question: Which field is based on ATT\&CK tactics in the context of malware behaviour?

> **Answer:** Objective

Question: What is the Identifier of "Create Process" micro-behavior?

> **Answer:** C0017

Question: What is the behaviour with an Identifier of B0009?

> **Answer:** Virtual Machine Detection

Question: Malware can be used to obfuscate data using base64 and XOR. What is the related micro-behavior for this?

> **Answer:** Encode Data

Question: Which micro-behavior refers to "Malware is capable of initiating HTTP communications"?

> **Answer:** HTTP Communication

***

## Task 5: Dissecting CAPA Results Part 3: Namespaces

CAPA groups its rules into Namespaces to keep related behaviors organized. The highest level of this hierarchy is the Top-Level Namespace (TLN).

* **anti-analysis:** Contains rules detecting evasion techniques like obfuscation, packing, and VM detection.
* **communication:** Pertains to network interactions, such as HTTP requests or C2 traffic.
* **persistence:** Focuses on how malware maintains a presence across reboots.
* **nursery:** A special staging ground for rules that are currently unpolished or in testing.

<figure><img src="../.gitbook/assets/image (29) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Underneath the TLN, sub-namespaces further categorize the behavior, such as `anti-vm/vm-detection` under the `anti-analysis` TLN.

An example of a CAPA name-space and capability output:

```bash
┌──────────────────────────────────────────────────────┬──────────────────────────────────────────────────────┐
│ Capability                                           │ Namespace                                            │
├──────────────────────────────────────────────────────┼──────────────────────────────────────────────────────┤
│ reference anti-VM strings                            │ anti-analysis/anti-vm/vm-detection                   │
│ reference anti-VM strings targeting VMWare           │ anti-analysis/anti-vm/vm-detection                   │
│ reference anti-VM strings targeting VirtualBox       │ anti-analysis/anti-vm/vm-detection                   │
│ contain obfuscated stackstrings (2 matches)          │ anti-analysis/obfuscation/string/stackstring         │
│ reference HTTP User-Agent string                     │ communication/http                                   │
│ check HTTP status code                               │ communication/http/client                            │
│ reference Base64 string                              │ data-manipulation/encoding/base64                    │
│ encode data using XOR                                │ data-manipulation/encoding/xor                       │
│ contain a thread local storage (.tls) section        │ executable/pe/section/tls                            │
│ get common file path                                 │ host-interaction/file-system                         │
│ create directory                                     │ host-interaction/file-system/create                  │
│ delete file                                          │ host-interaction/file-system/delete                  │
│ read file on Windows (4 matches)                     │ host-interaction/file-system/read                    │
│ write file on Windows (5 matches)                    │ host-interaction/file-system/write                   │
│ get thread local storage value                       │ host-interaction/process                             │
│ create process on Windows                            │ host-interaction/process/create                      │
│ allocate or change RWX memory                        │ host-interaction/process/inject                      │
│ reference cryptocurrency strings                     │ impact/cryptocurrency                                │
│ link function at runtime on Windows (5 matches)      │ linking/runtime-linking                              │
│ parse PE header (4 matches)                          │ load-code/pe                                         │
│ resolve function by parsing PE exports (186 matches) │ load-code/pe                                         │
│ run PowerShell expression                            │ load-code/powershell/                                │
│ schedule task via at                                 │ persistence/scheduled-tasks                          │
│ schedule task via schtasks                           │ persistence/scheduled-tasks                          │
└──────────────────────────────────────────────────────┴──────────────────────────────────────────────────────┘
```

Question: Which top-level Namespace contains a set of rules specifically designed to detect behaviours, including obfuscation, packing, and anti-debugging techniques exhibited by malware to evade analysis?

> **Answer:** anti-analysis

Question: Which namespace contains rules to detect virtual machine (VM) environments? Note that this is not the TLN or Top-Level Namespace.

> **Answer:** anti-vm/vm-detection

Question: Which Top-Level Namespace contains rules related to behaviours associated with maintaining access or persistence within a compromised system? This namespace is focused on understanding how malware can establish and maintain a presence within a compromised environment, allowing it to persist and carry out malicious activities over an extended period.

> **Answer:** persistence

Question: Which namespace addresses techniques such as String Encryption, Code Obfuscation, Packing, and Anti-Debugging Tricks, which conceal or obscure the true purpose of the code?

> **Answer:** obfuscation

Question: Which Top-Level Namespace Is a staging ground for rules that are not quite polished?

> **Answer:** Nursery

Question: Proceed to the next task for the 2nd part of the discussion!

> **Answer:** No answer needed

***

## Task 6: Dissecting CAPA Results Part 4: Capability

Capabilities are the specific actions CAPA has identified based on the matched YAML rules. There is a direct relationship between the Capability name and the underlying rule file. For instance, the capability "reference anti-VM strings targeting VMWare" maps directly to the rule file `reference-anti-vm-strings-targeting-vmware.yml`.

By examining these capabilities alongside their respective namespaces, an analyst can quickly piece together the exact strategy the malware is using to execute its payload or hide from defenders.

Question: What rule yaml file was matched if the Capability or rule name is check HTTP status code?

> **Answer:** check-http-status-code.yml

Question: What is the name of the Capability if the rule YAML file is reference-anti-vm-strings.yml?

> **Answer:** reference anti-VM strings

Question: Which TLN or Top-Level Namespace includes the Capability or rule name run PowerShell expression?

> **Answer:** load-code

Question: Check the conditions inside the check-for-windows-sandbox-via-registry.yml rule file from this link. What is the value of the API that ends in Ex is it looking for?

> **Answer:** RegOpenKeyEx

***

## Task 7: More Information, more fun!

While the standard output is incredibly useful, sometimes an investigation requires knowing exactly _why_ a rule was triggered. Running CAPA with the `-vv` parameter outputs the exact assembly instructions or strings that matched the rule condition.

<figure><img src="../.gitbook/assets/image (30) (1).png" alt=""><figcaption></figcaption></figure>

Because this level of verbosity can generate thousands of lines of output, the best approach is to output the results to a JSON file using the `-j` parameter. This JSON file can then be uploaded to the **CAPA Web Explorer**, a graphical interface that allows you to interactively filter, search, and examine the deep technical details using its Global Search Box.

Question: Which parameter allows you to output the result of CAPA into a .json file?

> **Answer:** -j

Question: What tool allows you to interactively explore CAPA results in your web browser?

> **Answer:** CAPA Web Explorer

Question: Which feature of this CAPA Web Explorer allows you to filter options or results?

> **Answer:** Global Search Box

***

## Task 8: Conclusion

CAPA plays a critical role in proactive threat hunting and incident response. By automating the extraction of capabilities via static analysis, it spares security experts from the laborious task of manual reverse engineering. This streamlined approach allows defenders to swiftly comprehend harmful software, enabling them to construct stronger defensive strategies and indicators of compromise (IoCs) with minimal delay.
