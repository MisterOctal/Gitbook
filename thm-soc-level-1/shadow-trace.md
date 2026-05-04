---
icon: user-ninja
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/66264cef7bba67a6bbbe7179-1759948787931
coverY: 0
coverHeight: 138
---

# Shadow Trace

**Date:** 04.05.2026

**Room Category:** Challenge

**Core Concept:** Analyzing a suspicious file, uncovering hidden clues, extracting Indicators of Compromise (IOCs), and tracing the source of an infection through alert correlation.

In this scenario, a suspicious file masquerading as a company updater is found on a user's machine, triggering EDR alerts. The objective is to perform basic SOC triage by analyzing the binary, gathering potential IOCs, and correlating the alerts to uncover the malicious behavior.

***

## Task 1: Scenario

The primary goal of this investigation is to halt a potential infection before it spreads further by piecing together the attacker's actions.

**Learning Objectives:**

* Extract IOCs from suspicious binaries.
* Correlate alerts with malicious activity.
* Perform basic SOC triage actions.

Question: Click here to start the challenge

> **Answer:** No answer needed

***

## Task 2: File analysis

The investigation begins with analyzing the binary located at `C:\Users\DFIRUser\Desktop\windows-update.exe`.

**Analysis Methodology:**

* **VirusTotal:** Utilized initially to quickly gather high-level information and community intelligence on the file's hash.
* **PEStudio:** Used for an in-depth static analysis of the executable. This tool successfully identified the architecture (64-bit) and revealed the imported libraries, including socket communication DLLs like `WS2_32.dll`.
* **String Extraction:** To locate embedded URLs and domains, the native strings utility was piped into `findstr` using the following command:

```powershell
strings .\windows-update.exe | findstr.exe 'tryhatme'
```

* **Decoding:** CyberChef was used to decode the identified Base64 strings to retrieve the hidden flag.

Question: What is the architecture of the binary file windows-update.exe?

> **Answer:** 64-bit

Question: What is the hash (sha-256) of the file windows-update.exe?

> **Answer:** b2a88de3e3bcfae4a4b38fa36e884c586b5cb2c2c283e71fba59efdb9ea64bfc

Question: Identify the URL within the file to use it as an IOC

> **Answer:** http://tryhatme.com/update/security-update.exe

Question: With the URL identified, can you spot a domain that can be used as an IOC?

> **Answer:** responses.tryhatme.com

Question: Input the decoded flag from the suspicious domain

> **Answer:** THM{you\_g0t\_some\_IOCs\_friend}

Question: What library related to socket communication is loaded by the binary?

> **Answer:** WS2\_32.dll

***

## Task 3: Alerts Analysis

The second phase involves reviewing the triggered alerts to reconstruct the attack timeline and identify further malicious web requests.

**Analysis Methodology:** The alert investigation primarily relied on manual review and decoding. CyberChef was the only tool required for this phase, utilized specifically to decode obfuscated command lines utilizing Base64 and Decimal/ASCII encoding techniques.

Question: Can you identify the malicious URL from the trigger by the process powershell.exe?

> **Answer:** https://tryhatme.com/dev/main.exe

Question: Can you identify the malicious URL from the alert triggered by chrome.exe?

> **Answer:** https://reallysecureupdate.tryhatme.com/update.exe

Question: What's the name of the file saved in the alert triggered by chrome.exe?

> **Answer:** test.txt

***

## Conclusion

The Shadow Trace challenge successfully demonstrates the fundamental workflow of SOC triage and basic malware analysis. By combining static analysis tools like PEStudio and VirusTotal with command line utilities (`strings`, `findstr`) and decoding platforms like CyberChef, analysts can effectively extract critical IOCs from malicious binaries. Correlating these extracted indicators with EDR alerts provides a clear picture of the attack chain.
