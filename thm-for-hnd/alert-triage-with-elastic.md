---
icon: user-hat-tie-magnifying-glass
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1760377064601
coverY: 0
coverHeight: 140
---

# Alert Triage With Elastic

**Date:** 16.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Utilizing Kibana and the Elastic Stack to triage security alerts, analyze web logs, and correlate Windows Security and Sysmon events to reconstruct a full attack timeline.

As a Security Operations Center (SOC) analyst, investigating alerts requires finding and correlating clear evidence across multiple log sources. This document outlines the process of using Elastic (Kibana) to perform alert triage, identifying indicators of compromise (IoCs), and linking web exploitation to host-based lateral movement and data exfiltration.

***

## Task 1: Introduction

This guided challenge focuses on performing alert triage and initial investigations on suspicious activity occurring within an IIS and Windows server environment (belonging to a client named SomeCorp). The objective is to determine if the alerts represent malicious activity by reconstructing the attack sequence.

Question: I understand the learning objectives and am ready to investigate with Elastic!

> **Answer:** No answer needed

***

## Task 2: Scenario Briefing

<figure><img src="../.gitbook/assets/image (391).png" alt=""><figcaption></figcaption></figure>

Suspicious activity has triggered multiple alerts in the SOC dashboard regarding SomeCorp's infrastructure (e.g., Web Requests Indicating File Upload, GET Requests to ASPX File with Query Parameters, Administrator Access Outside Business Hours).

The initial step in triage is to properly configure the Kibana dashboard:

1. **Access Kibana:** Activate the VM an access the lab
2. **Data View:** Set to `Alert Triage With Elastic`
3. **Time Range:** Set to `Entire data range`

<figure><img src="../.gitbook/assets/image (393).png" alt=""><figcaption></figcaption></figure>

To filter specifically for the IIS web server logs, the first query is utilized:

```bash
_index:weblogs
```

Question: How many logs are available for analysis within the entire time range?

> **Answer:** 1467

Question: What is the field value for the client.ip in the weblogs index?

> **Answer:** 203.0.113.55

***

## Task 3: Investigating Web Attacks

<figure><img src="../.gitbook/assets/image (394).png" alt="" width="360"><figcaption></figcaption></figure>

The first high-severity alert indicates that the IP address `203.0.113.55` made multiple POST requests to `proxyLogon.ecp`. To investigate this, we filter the web logs for the specific IP and HTTP method:

```bash
_index:weblogs 
and client.ip:203.0.113.55 
and http.request.method:POST
```

<figure><img src="../.gitbook/assets/image (396).png" alt=""><figcaption></figcaption></figure>

_Note: Adding columns like `client.ip`, `user.agent`, `http.request.method`, `url.path`, and `http.response.status_code` helps structure the data._

Further investigation of the user-agent and requested path reveals this is automated activity related to the **ProxyLogon** vulnerability.

<figure><img src="../.gitbook/assets/image (397).png" alt="" width="361"><figcaption></figcaption></figure>

A subsequent alert flags GET requests to `errorEE.aspx` with the `cmd=` parameter. This parameter is a hallmark of web shell activity, allowing attackers to append and execute OS commands directly via the URL (e.g., `cmd=whoami`). We query this specific activity and sort from Old-New:

```bash
_index:weblogs 
and client.ip:203.0.113.55 
and http.request.method:GET 
and errorEE.aspx
```

<figure><img src="../.gitbook/assets/image (399).png" alt=""><figcaption></figcaption></figure>

The results inside the `url.path` field confirm that system commands were successfully passed to the web shell, verifying a breach (True Positive).

Question: How many POST requests did the IP address 203.0.113.55 make to proxyLogon.ecp?

> **Answer:** 3

Question: Which user.agent paired with the IP address 203.0.113.55 made the POST requests?

> **Answer:** python-requests/2.25.1

Question: How many logs contain the cmd= query parameter in the url.path field?

> **Answer:** 20

Question: Which command was run utilizing errorEE.aspx on Jul 20, 2025 @ 04:45:50.000?

> **Answer:** hostname

***

## Task 4: Uncovering Account Activity

<figure><img src="../.gitbook/assets/image (400).png" alt="" width="361"><figcaption></figcaption></figure>

After confirming web exploitation, the investigation pivots to host-based evidence. An alert shows the `Administrator` account accessing the server outside business hours. We investigate Windows Security **Event ID 4624 (Logon)** to confirm when, how, and from where this occurred.

```bash
@timestamp >= "2025-07-20T05:11:22" 
and winlog.event_id:4624 
and host.name:winserv2019.some.corp 
and winlog.event_data.TargetUserName:Administrator
```

<figure><img src="../.gitbook/assets/image (402).png" alt=""><figcaption></figcaption></figure>

_Key columns to add: `winlog.event_id`, `host.name`, `winlog.event_data.TargetUserName`, `winlog.logon.type`, `winlog.event_data.IpAddress`._

This confirms an Administrator logon from the attacker's IP (`203.0.113.55`). Next, we correlate this with Sysmon **Event ID 1 (Process Creation)** to see what happened post-logon:

```bash
@timestamp >= "2025-07-20T05:11:22" 
and winlog.event_id:1 
and user.name:Administrator
```

<figure><img src="../.gitbook/assets/image (403).png" alt=""><figcaption></figcaption></figure>

Following the logon, a critical alert flags "New User Account Created". We investigate Windows Security logs specifically looking at the `winlog.task` field for User Account Management events (which correlates to **Event ID 4720**):

```bash
@timestamp >= "2025-07-20T05:13:10.000" 
and winlog.channel:Security 
and winlog.task:User Account Management
```

Question: What is the winlog.record\_id of the Administrator 4624 logon event?

> **Answer:** 17166

Question: What is the process.pid of the Sysmon 1 event that occurred on Jul 20, 2025 @ 05:11:27.996?

> **Answer:** 964

Question: What is the winlog.event\_id for the new user account being created?

> **Answer:** 4720

Question: What is the name of the new user account?

> **Answer:** svc\_backup

***

## Task 5: Exposing Command Execution

<figure><img src="../.gitbook/assets/image (404).png" alt="" width="361"><figcaption></figcaption></figure>

An alert flags suspicious command-line usage (`C:\Windows\system32\cmd.exe`) by the Administrator. We query Sysmon for child processes launched by `cmd.exe`:

```bash
@timestamp >= "2025-07-20T05:13:15" 
and process.parent.name:cmd.exe 
and user.name:Administrator
```

<figure><img src="../.gitbook/assets/image (405).png" alt=""><figcaption></figcaption></figure>

Adding `process.command_line` to the table reveals exactly what the attacker typed. We observe the creation of the `svc_backup` account and its addition to specific groups. We can correlate this command execution with Windows Security **Event ID 4732 (Security Group Management)** to verify the group modifications:

```bash
@timestamp >= "2025-07-20T05:13:15" 
and (winlog.event_id:4732 or process.parent.name:cmd.exe)
```

<figure><img src="../.gitbook/assets/image (406).png" alt=""><figcaption></figcaption></figure>

To see if the attack continued beyond standard CMD usage, we check PowerShell logs (**Event ID 4104**):

```bash
@timestamp >= "2025-07-20T05:13:15" 
and event.module:powershell 
and event.code:4104
```

<figure><img src="../.gitbook/assets/image (407).png" alt=""><figcaption></figcaption></figure>

_Adding the field `powershell.file.script_block_text` displays the plaintext PowerShell commands._

Finally, to confirm data exfiltration staging, we manually search for a known archiving tool used by the client that did _not_ trigger a direct alert:

```bash
process.name: "Rar.exe"
```

Question: What command does the attacker use to add the new account to the "Remote Desktop Users" group?

> **Answer:** net localgroup "Remote Desktop Users" svc\_backup /add

Question: What is the winlog.record\_id of the 4732 Security event when the attacker adds the user to the Administrator group?

> **Answer:** 17254

Question: What PowerShell command did the attacker run on Jul 20, 2025 @ 05:16:14.628?

> **Answer:** net group "Domain Admins" /domain

Question: What is the name of the archive that the attacker creates using the Rar.exe executable?

> **Answer:** finance\_it\_archive.rar

***

## Conclusion

This guided challenge demonstrated the full workflow of triaging a complex, multi-stage attack using Elastic and Kibana. By starting with a web application alert (ProxyLogon), the investigation successfully pivoted to host-based logs (Windows Event Logs and Sysmon), uncovering an RDP session, the creation of a persistent backdoor user, privilege escalation, and the staging of data for exfiltration using native tools.

***

## Summary of Elastic (KQL) Queries Used

| Query                                                                                                                                                 | Investigation Phase / Use Case                                                                                 |
| ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `_index:weblogs`                                                                                                                                      | Initial filtering to isolate IIS web server logs.                                                              |
| `_index:weblogs and client.ip:203.0.113.55 and http.request.method:POST`                                                                              | Isolating specific POST requests from the attacker's IP (identifying ProxyLogon attempts).                     |
| `_index:weblogs and client.ip:203.0.113.55 and http.request.method:GET and errorEE.aspx`                                                              | Hunting for GET requests targeting the known web shell, revealing commands passed via the `cmd=` parameter.    |
| `@timestamp >= "2025-07-20T05:11:22" and winlog.event_id:4624 and host.name:winserv2019.some.corp and winlog.event_data.TargetUserName:Administrator` | Verifying successful interactive logons (Event ID 4624) by the Administrator account post-breach.              |
| `@timestamp >= "2025-07-20T05:11:22" and winlog.event_id:1 and user.name:Administrator`                                                               | Using Sysmon (Event ID 1) to identify the process creation chain immediately following the RDP logon.          |
| `@timestamp >= "2025-07-20T05:13:10.000" and winlog.channel:Security and winlog.task:User Account Management`                                         | Detecting new user account creation (Event ID 4720) established by the attacker for persistence.               |
| `@timestamp >= "2025-07-20T05:13:15" and process.parent.name:cmd.exe and user.name:Administrator`                                                     | Exposing explicit commands executed in the command line by tracking child processes spawned from `cmd.exe`.    |
| `@timestamp >= "2025-07-20T05:13:15" and (winlog.event_id:4732 or process.parent.name:cmd.exe)`                                                       | Correlating command-line activity with Windows Security Group Management events (Event ID 4732).               |
| `@timestamp >= "2025-07-20T05:13:15" and event.module:powershell and event.code:4104`                                                                 | Extracting plaintext PowerShell commands using Script Block Logging (Event ID 4104).                           |
| `process.name: "Rar.exe"`                                                                                                                             | Proactively hunting for signs of data staging/archiving using a legitimate tool that bypassed standard alerts. |
