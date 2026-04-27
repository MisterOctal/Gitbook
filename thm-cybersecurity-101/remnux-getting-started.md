---
icon: linux
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1729043928499.png
coverY: 0
coverHeight: 138
layout:
  width: default
  cover:
    visible: true
    size: full
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
---

# REMnux: Getting Started

**Date:** 05.04.2026

**Room Category:** Walkthrough

**Core Concept:** Utilizing the REMnux virtual machine for malware analysis, including malicious document inspection, network simulation, and memory forensics preprocessing.

Analyzing potentially malicious software can be highly stressful during an active security incident. Analysts need reliable, isolated environments and precise tools to ensure their findings are accurate and safe. This room introduces the REMnux VM, a specialized Linux distribution packed with pre-installed reverse engineering and malware analysis tools like Volatility, YARA, Wireshark, oledump, and INetSim.

***

## Task 1: Introduction

REMnux acts as a dedicated laboratory. It provides a secure, sandbox-like environment to dissect malicious software without risking your primary operating system. Because it comes pre-configured, it saves analysts from the significant hassle of manually installing dependencies and resolving tool conflicts. The goal here is to explore its capabilities across document analysis, network simulation, and memory forensics.

<figure><img src="../.gitbook/assets/image (14) (1) (1) (1) (1) (1).png" alt="" width="166"><figcaption></figcaption></figure>

Question: Proceed with the next tasks to learn more!

> **Answer:** No answer needed

***

## Task 2: Machine Access

In this environment, you utilize two machines: the target REMnux virtual machine and an AttackBox. Deploying the machine provides a split-screen view where you can run the terminal commands. Almost all the files required for this room are conveniently located in the `Desktop/tasks` directory.

Question: I'm excited to learn more about the tools inside the REMnux VM!

> **Answer:** No answer needed

***

## Task 3: File Analysis

Threat actors frequently hide malicious code inside Office documents. To safely examine these files, we use `oledump.py`. This Python tool analyzes OLE2 files (Structured Storage or Compound File Binary Format), which Microsoft uses for documents, spreadsheets, and presentations.

By running `oledump.py agenttesla.xlsm`, we can see the internal data streams of the Excel file. An "M" indicator next to a stream means a Macro is present. We can extract this specific stream using the `-s` parameter (e.g., `-s 4`) and make it readable using the `--vbadecompress` parameter.

During the exercise, analyzing the decompressed VBA macro reveals a heavily obfuscated PowerShell script. By pasting this script into CyberChef and using the "Find/Replace" operation to remove wildcard characters like `*` and `^`, the true intent of the script is exposed. It attempts to download a malicious executable using `Invoke-WebRequest` and saves it to a temporary location (`$TempFile`) before executing it.

<figure><img src="../.gitbook/assets/image (15) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: What Python tool analyzes OLE2 files, commonly called Structured Storage or Compound File Binary Format?

> **Answer:** oledump.py

Question: What tool parameter we used in this task allows you to select a particular data stream of the file we are using it with?

> **Answer:** -s

Question: During our analysis, we were able to decode a PowerShell script. What command is commonly used for downloading files from the internet?

> **Answer:** Invoke-WebRequest

Question: What file was being downloaded using the PowerShell script?

> **Answer:** Doc-3737122pdf.exe

Question: During our analysis of the PowerShell script, we noted that a file would be downloaded. Where will the file being downloaded be stored?

> **Answer:** $TempFile

Question: Using the tool, scan another file named possible\_malicious.docx located in the /home/ubuntu/Desktop/tasks/agenttesla/ directory. How many data streams were presented for this file?

> **Answer:** 16

Question: Using the tool, scan another file named possible\_malicious.docx located in the /home/ubuntu/Desktop/tasks/agenttesla/ directory. At what data stream number does the tool indicate a macro present?

> **Answer:** 8

***

## Task 4: Fake Network to Aid Analysis

Malware often tries to communicate with a Command and Control (C2) server or download secondary payloads. We want to observe this behavior without allowing the malware to access the real internet. INetSim (Internet Services Simulation Suite) solves this by simulating common internet services like HTTP, HTTPS, DNS, and FTP.

By editing the `/etc/inetsim/inetsim.conf` file, we can configure INetSim to bind its DNS service to our local machine's IP address. Once INetSim is running, any HTTP request made by the malware is intercepted, and INetSim serves a fake file in response. We test this by using `wget` to download a simulated payload, which successfully connects to our fake network. Afterwards, analyzing the INetSim report in `/var/log/inetsim/report/` allows us to see exactly what URLs the malware attempted to contact.

Question: Download and scan the file named flag.txt from the terminal using the command sudo wget https://MACHINE\_IP/flag.txt --no-check-certificate. What is the flag?

> **Answer:** Tryhackme{remnux\_edition}

Question: After stopping the inetsim, read the generated report. Based on the report, what URL Method was used to get the file flag.txt?

> **Answer:** GET

***

## Task 5: Memory Investigation: Evidence Preprocessing

Analyzing memory dumps is a core component of Digital Forensics and Incident Response (DFIR). Volatility 3 is an advanced framework used to extract artifacts from volatile memory (RAM). Because running Volatility plugins can take several minutes each, standard practice involves "preprocessing" the memory image. This means running a loop of multiple plugins and saving the output to text files, allowing analysts to quickly search through the results later.

Key Windows plugins include:

* **PsTree:** Lists processes in a tree structure based on parent process IDs.
* **PsList:** Lists all currently active processes.
* **CmdLine:** Shows the command line arguments used when a process was launched.
* **Malfind:** Identifies process memory ranges that potentially contain injected, hidden malicious code.
* **DllList:** Lists loaded modules and libraries.

Alongside Volatility, the Linux `strings` utility is used to extract printable text (ASCII, 16-bit little-endian, and 16-bit big-endian) from the raw memory image, revealing hidden IP addresses, file paths, or registry keys.

Question: What plugin lists processes in a tree based on their parent process ID?

> **Answer:** PsTree

Question: What plugin is used to list all currently active processes in the machine?

> **Answer:** PsList

Question: What Linux utility tool can extract the ASCII, 16-bit little-endian, and 16-bit big-endian strings?

> **Answer:** strings

Question: By running vol3 with the Malfind parameter, what is the first (1st) process identified suspected of having an injected code?

> **Answer:** csrss.exe

Question: Continuing from the previous question (Question 4), what is the second (2nd) process identified suspected of having an injected code?

> **Answer:** winlogon.exe

Question: By running vol3 with the DllList parameter, what is the file path or directory of the binary @WanaDecryptor@.exe?

> **Answer:** C:\Intel\ivecuqmanpnirkt615

***

## Conclusion

This room provided a highly practical introduction to the REMnux distribution. By extracting malicious macros with `oledump.py`, safely capturing malicious network traffic using INetSim, and preprocessing memory dumps using Volatility 3 and `strings`, the foundational skills for static and dynamic malware analysis have been established. REMnux remains an indispensable environment for anyone stepping into malware analysis or incident response.
