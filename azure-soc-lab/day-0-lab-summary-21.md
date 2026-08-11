---
icon: bullseye-arrow
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 21 - MITRE Mapping

**Date:** 11.08.2026

**Core Concept**: MITRE ATT\&CK technique discovery and threat actor profiling through honeypot log analysis

With the Cowrie dataset exported and archived, analysis focused on mapping detected attack patterns to the MITRE ATT\&CK framework. KQL queries identified 16 distinct techniques across multiple tactic categories. A comprehensive set of reconnaissance, credential access, and post-exploitation techniques were confirmed across the dataset. The full Cowrie JSON logs (May 24 - Aug 2, 17.9 MB) were also exported for offline analysis and public archival.

Screenshots for all KQL queries ran today, all queries ran today, and my recovered Cowrie data have also been pushed to github.

***

## Section 1: Reconnaissance Techniques

**T1592 - Gather Victim Host Information**

Architecture fingerprinting commands dominated reconnaissance activity.

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("uname", "cpuinfo", "lscpu", "free", "cat /proc")
| project src_ip, session, input
```

Multiple attackers executed `uname -s -v -n -m` to extract OS version, architecture, and hostname. Commands targeting `/proc/cpuinfo` confirmed attempts to enumerate CPU capabilities. This indicates systematic profiling of target systems to select appropriate payloads based on architecture.

**T1083 - File and Directory Discovery**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("ls", "find", "locate", "dir")
| project src_ip, session, input
```

Basic directory enumeration via `ls` and `find` commands appeared across multiple sessions, confirming standard filesystem reconnaissance patterns.

**T1087 - Account Discovery**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("id", "whoami", "getent", "cat /etc/passwd")
| project src_ip, session, input
```

User enumeration via `id`, `whoami`, and `/etc/passwd` reading confirmed in multiple sessions. Attackers consistently checked their execution context after gaining access.

**T1580 - Cloud Infrastructure Discovery**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("nvidia", "gpu", "cuda", "cpus", "lscpu")
| project src_ip, session, input
```

Explicit GPU and CPU detection attempts appeared in the dataset, confirming resource profiling for potential cryptomining or compute-intensive payload deployment.

***

## Section 2: Credential Access Techniques

**T1110.003 - Brute Force - Password Spraying**

```kql
cowrie_CL
| where eventid == "cowrie.login.failed"
| summarize FailedAttempts = count() by src_ip
| where FailedAttempts > 10
| project src_ip, FailedAttempts
```

103.82.210.181 was responsible for 1431 failed login attempts, the single most aggressive credential spraying source. The next tier included 37.111.53.110 (1396 attempts), 103.204.167.40 (891 attempts), and 91.92.42.87 (766 attempts). This distributed pattern indicates either a coordinated botnet or multiple independent scanning operations.

**T1110.004 - Brute Force - Credential Stuffing**

```kql
cowrie_CL
| where eventid == "cowrie.login.failed"
| summarize UsernamesTried = dcount(username) by password
| where UsernamesTried > 5
| project password, UsernamesTried
```

The password "123456" was attempted against 69 distinct usernames. "123" was tried against 43 usernames. Other commonly attempted passwords included "1234" (39 usernames), "12345678" (32 usernames), and "12345" (28 usernames). This confirms dictionary-based credential stuffing rather than targeted attacks based on prior intelligence.

***

## Section 3: Persistence and Access

**T1098 - Account Manipulation**

```kql
cowrie_CL
| where eventid == "cowrie.login.success"
| summarize SessionCount = dcount(session) by username, password, src_ip
| where SessionCount > 1
| project username, password, src_ip, SessionCount
```

Successful logins were rare but reused across multiple sessions. root/123456 was used in 3 sessions (195.178.110.228, 176.65.132.8, 195.178.110.137, 91.92.40.18). admin/admin123 appeared in 2 sessions. deploy/deploy logged in twice from 77.90.185.20. This indicates attackers reusing discovered credentials across multiple access attempts.

***

## Section 4: Defense Evasion Techniques

**T1222.002 - File and Directory Permissions Modification**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("chattr", "chmod", "chown")
| project src_ip, session, input
```

File permission modification commands confirmed across multiple sessions, indicating attempts to manipulate access controls on target systems.

**T1622 - Debugger Evasion**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("echo", "test", "sh -c", "/dev/null")
| project src_ip, session, input
```

Shell behavior testing via echo, test, and /dev/null redirection appeared in the dataset. These commands can be used to detect execution environment differences, a technique for identifying sandboxes or monitoring systems.

***

## Section 5: Command and Control

**T1071.001 - Application Layer Protocol - HTTP**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("wget", "curl") and input has "http"
| project src_ip, session, input
```

HTTP-based payload delivery via wget and curl confirmed across 6 distinct sessions. Attackers downloaded files from 91.199.133.133:8080/deploy.sh and 31.56.209.153/bins.sh, among others.

**T1008 - Fallback Channels**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("wget", "curl", "/dev/tcp", "python")
| summarize MethodCount = dcount(input) by session, src_ip
| where MethodCount > 2
| project session, src_ip, MethodCount
```

One session (a6ae8bbbe44e, source 91.92.40.18) used 7 distinct delivery methods including wget, curl, and /dev/tcp socket connections, indicating sophisticated redundancy for payload delivery across restricted environments.

**T1105 - Ingress Tool Transfer**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("-O", "| sh", "> /tmp/", "| bash")
| project src_ip, session, input
```

Multiple payload delivery patterns confirmed: wget with -O flag for file output, piping to sh for direct execution, and redirection to /tmp/ for staged deployment.

**T1571 - Non-Standard Port**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input contains ":" and input has_any ("wget", "curl")
| project src_ip, session, input
```

Port specifications in download commands appeared, indicating use of non-standard ports for C2 communication.

***

## Section 6: Execution Techniques

**T1651 - Stage Capabilities**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("echo -ne", "echo -e", "printf", "\\x")
| project src_ip, session, input
```

Hex-encoded payload staging via echo -ne and printf confirmed across multiple sessions. Attackers used escaped byte sequences (\x notation) to construct binary data piece by piece, a technique for evading string-based detection.

**T1204.002 - User Execution - Malicious File**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("./", "bash /tmp/", "sh /tmp/")
| project src_ip, session, input
```

Downloaded malicious files were executed via ./ prefix and bash/sh wrappers pointing to /tmp/ staging directories.

***

## Section 7: Impact Techniques

**T1496 - Resource Hijacking**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("xmrig", "mining", "stratum", "monero", "gpu")
| project src_ip, session, input
```

No explicit cryptominer invocations detected in command logs. However, the systematic GPU and CPU profiling confirmed in earlier queries strongly suggests cryptomining as the final payload objective.

***

## Section 8: Primary Attack Patterns

The most aggressive attackers were distributed across multiple IPs rather than concentrated in a single source. 103.82.210.181 led with 1431 failed login attempts, but subsequent attackers showed comparable activity levels, indicating either a large botnet with distributed command infrastructure or multiple independent scanning operations using similar tools.

Successful compromises were rare (6 total successful logins), but when they occurred, attackers consistently attempted to gather system information via uname, cpuinfo, and GPU enumeration. This profiling-then-payload pattern was consistent across sessions, suggesting organized infrastructure rather than ad-hoc attacks.

***

## Section 9: Data Recovery and Archival

The complete Cowrie JSON log export (May 24 - Aug 2, 2026) was successfully recovered and compressed. File size: 17.9 MB (all rotated logs plus active log). scp was used to exfiltrate all data onto my host device.

No traffic was captured in August 2026 despite the honeypot remaining online and NSG rules remaining open. Attack volume dried out after Aug 2, 09:53 UTC. The timestamp corruption from the Azure Monitor Agent ingestion pipeline means historical event timestamps cannot be trusted for timeline analysis, limiting the ability to correlate attack phases across days. This likely occured to a plethora of reasons ranging from AMA interference to shodan or other crawlers discouraging attacks onto the honeypot.

***

## Conclusion

Day 21 identified clear attack patterns: distributed credential spraying infrastructure probing for weak SSH passwords, followed by systematic reconnaissance of successful compromises to profile system resources. While a single sophisticated multi-stage malware session was captured earlier (Day 19), the broader dataset shows commodity-grade automated scanning and dictionary-based credential attacks. The lack of explicit cryptominer commands in logs combined with deliberate GPU/CPU enumeration suggests the final payloads were staged outside the honeypot or the compromises occurred too late in the attack chain to be captured. Day 22 will focus on infrastructure cleanup and project retrospective.
