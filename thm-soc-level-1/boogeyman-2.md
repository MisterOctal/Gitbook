---
icon: ghost
cover: >-
  https://cdn.discordapp.com/attachments/1498609087838158952/1506600634722287626/image.png?ex=6a0edaae&is=6a0d892e&hm=8057aa5a58a713b59715e456070d777b8c851bd6ec7c9298779e6698e56ce89e
coverY: 0
coverHeight: 138
---

# Boogeyman 2

**Date:** 20.05.2026&#x20;

**Room Category:** Challenge&#x20;

**Core Concept:** Phishing analysis, malicious macro extraction, memory forensics using Volatility, and identifying persistence mechanisms.

After a severe attack from the Boogeyman, Quick Logistics LLC improved its security defences. However, the threat actor has returned with new and improved tactics, techniques, and procedures. This investigation focuses on analysing the latest methods used by the Boogeyman threat group. The analysis requires a combination of email inspection, macro extraction, and volatile memory forensics.

***

## Task 1: Introduction

Quick Logistics LLC faced another incident despite previous security enhancements. The infamous Boogeyman group has returned, this time targeting the human resources department. This room requires analysing a phishing email and a memory dump from the victim's workstation.

<figure><img src="../.gitbook/assets/image (418).png" alt="" width="375"><figcaption></figcaption></figure>

Several tools are available in the provided environment. Volatility is an open-source framework for extracting digital artefacts from volatile memory samples. Olevba is a tool for analysing and extracting VBA macros from Microsoft Office documents.

Question: I am now ready for round 2 with the Boogeyman!

> **Answer:** No answer needed

***

## Task 2: Spear Phishing Human Resources

Maxine, a Human Resource Specialist working for Quick Logistics LLC, received an application for an open position within the company. Unbeknownst to her, the attached resume was malicious and successfully compromised her workstation.

<figure><img src="../.gitbook/assets/image (419).png" alt="" width="563"><figcaption></figcaption></figure>

The security team flagged several suspicious commands executed on Maxine's machine, which prompted the initial investigation. Analysts must now assess the full impact of this compromise by examining the provided artefacts.

Question: What email was used to send the phishing email?

> **Answer:** westaylor23@outlook.com

Question: What is the email of the victim employee?

> **Answer:** maxine.beck@quicklogisticsorg.onmicrosoft.com

Question: What is the name of the attached malicious document?

> **Answer:** Resume\_WesleyTaylor.doc

Question: What is the MD5 hash of the malicious attachment?

```bash
md5sum Resume_WesleyTaylor.doc
```

> **Answer:** 52c4384a0b9e248b95804352ebec6c5b

After extracting the hash of the malicious document, the hash was uploaded onto VirusTotal to quickly extract certain information. However, olevba can still be used to extract information, and some information such as the updated PID can only be extracted by olevba.

<figure><img src="../.gitbook/assets/image (420).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (422).png" alt=""><figcaption></figcaption></figure>

Question: What URL is used to download the stage 2 payload based on the document's macro?

> **Answer:** https://files.boogeymanisback.lol/aa2a9c53cbb80416d3b47d85538d9971/update.png

```bash
olevba Resume_WesleyTaylor.doc
```

Question: What is the name of the process that executed the newly downloaded stage 2 payload?

> **Answer:** wscript.exe

```bash
vol -f WKSTN-2961.raw windows.pstree
```

Question: What is the full file path of the malicious stage 2 payload?

> **Answer:** C:\ProgramData\update.js

```bash
vol -f WKSTN-2961.raw windows.psscan
```

Question: What is the PID of the process that executed the stage 2 payload?

> **Answer:** 4260

Question: What is the parent PID of the process that executed the stage 2 payload?

> **Answer:** 1124

Question: What URL is used to download the malicious binary executed by the stage 2 payload?

> **Answer:** https://files.boogeymanisback.lol/aa2a9c53cbb80416d3b47d85538d9971/update.exe

Question: What is the PID of the malicious process used to establish the C2 connection?

> **Answer:** 6216

Question: What is the full file path of the malicious process used to establish the C2 connection?

> **Answer:** C:\Windows\Tasks\updater.exe

```bash
vol -f WKSTN-2961.raw windows.cmdline | grep 6216
```

Question: What is the IP address and port of the C2 connection initiated by the malicious binary? (Format: IP address:port)

> **Answer:** 128.199.95.189:8080

```bash
vol -f WKSTN-2961.raw windows.netscan | grep updater.exe
```

Question: What is the full file path of the malicious email attachment based on the memory dump?

> **Answer:** C:\Users\maxine.beck\AppData\Local\Microsoft\Windows\INetCache\Content.Outlook\WQHGZCFI\Resume\_WesleyTaylor (002).doc

```bash
vol -f WKSTN-2961.raw windows.cmdline | grep doc
```

Question: The attacker implanted a scheduled task right after establishing the c2 callback. What is the full command used by the attacker to maintain persistent access?

> **Answer:** schtasks /Create /F /SC DAILY /ST 09:00 /TN Updater /TR 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NonI -W hidden -c "IEX (\[Text.Encoding]::UNICODE.GetString(\[Convert]::FromBase64String((gp HKCU:\Software\Microsoft\Windows\CurrentVersion debug).debug)))"'

```bash
strings WKSTN-2961.raw | grep schtasks
```
