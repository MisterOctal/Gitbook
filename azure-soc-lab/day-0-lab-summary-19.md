---
icon: user-pilot-tie
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 19 - KQL 3

**Date:** 02.08.2026

**Core Concept**: KQL Analysis and Live Attack Chain Discovery

With the Cowrie-to-Sentinel pipeline confirmed working on Day 18, today's focus was deeper KQL analysis against the honeypot dataset. This included verifying the dataset's real scope, running a broader set of analytical queries, and confirming external threat intelligence for the top attacking IP, which unexpectedly turned into a genuine attack chain discovery rather than routine practice.

***

## Section 1: Overview

The day began by forcing a fresh ingestion cycle to pick up any Cowrie activity accumulated since Day 18, followed by verifying the dataset genuinely spans a wide range of source IPs and sessions rather than a narrow slice of traffic. From there, a deeper set of KQL queries was run across the dataset, one of which surfaced a session containing a full multi-stage malware deployment attempt, independently confirmed against VirusTotal.

<figure><img src="../.gitbook/assets/image (442).png" alt=""><figcaption></figcaption></figure>

***

## Section 2: Fresh Ingestion Check

The AMA service was restarted to force Fluent Bit to pick up any new Cowrie activity since Day 18, consistent with the restart-based workaround identified for this pipeline's file-tailing limitation.

```kql
cowrie_CL
| where ingestion_time() > ago(1h)
| count
```

<figure><img src="../.gitbook/assets/image (443).png" alt=""><figcaption></figcaption></figure>

Only 4 new events landed since the last restart, which is expected given the weird AMA parsing issues. This is a known and acknowledged limitation that wasn't worth fixing given the time generation field itself is also buggy.

***

## Section 3: Dataset Scope Verification

Since `TimeGenerated` cannot be trusted due to the timestamp limitation identified on Day 18, dataset scope was verified through session and IP diversity instead of date range.

```kql
cowrie_CL
| where eventid == "cowrie.session.connect"
| summarize SessionCount = dcount(session), UniqueIPs = dcount(src_ip)
```

<figure><img src="../.gitbook/assets/image (444).png" alt=""><figcaption></figcaption></figure>

The dataset returned 317 unique source IPs and a correspondingly large number of distinct sessions, the large majority of which involved only one or two commands, consistent with typical scanner/bot behavior rather than genuine interactive attacker sessions. This spread confirms the ingested data represents broad, varied honeypot activity rather than a narrow slice from one or two isolated incidents.

***

## Section 4: Notable Session Discovery

While reviewing session activity, one session stood out immediately for its command count relative to the rest of the dataset.

```kql
cowrie_CL
| where session == "a6ae8bbbe44e"
| where eventid == "cowrie.command.input"
```

<figure><img src="../.gitbook/assets/image (446).png" alt=""><figcaption></figcaption></figure>

Where the overwhelming majority of sessions logged only one or two commands, this session logged 26, immediately marking it as distinct from routine scanner noise and worth a full manual review.

***

## Section 5: Full Attack Chain Breakdown

Reviewing the full 26-command sequence revealed a genuine multi-stage malware deployment attempt, not a manual attacker typing commands by hand, but an automated dropper script executing a structured, redundant deployment routine.

**Stage 1 - Reconnaissance:** The session opened with basic environment fingerprinting:

```
cat /proc/cpuinfo
```

gathering CPU architecture information, almost certainly to select the correct malware binary variant for the target's architecture.

**Stage 2 - Redundant Payload Retrieval, Multiple Fallback Methods:** The script attempted three separate delivery methods in sequence, each designed to succeed under different environment restrictions:

1. `wget` with the `-O` flag to save the payload to a file (`/tmp/.ds`), then execute it
2. `wget` piping directly to `sh` via stdout, for environments where writing to disk might be restricted or monitored
3. A raw `/dev/tcp` socket connection with a manually crafted HTTP GET request, for environments where `wget` itself might be unavailable, targeting a binary named `real_x86_64`, explicitly matching the host's fingerprinted CPU architecture

This fallback chain is a deliberate resilience technique, ensuring the payload lands successfully across a range of restricted or non-standard Linux environments, exactly the kind of environment a honeypot or a hardened IoT device might present.

**Stage 3 - Obfuscated Script Construction:** Several commands used `echo -ne` with hex-escaped byte sequences to write out script content piece by piece into a file named `.dl`, rather than writing the script in plain text. This is a basic but genuine obfuscation technique intended to evade simple string-based detection of the script's actual content.

**Stage 4 - Anti-Analysis Behavior:** One command executed a loop scanning every running process under `/proc/[0-9]*`, checking each process's memory maps for references to shared libraries. Processes without any shared library references were treated as suspicious and killed. This is a deliberate evasion technique targeting statically-linked or minimal processes, a category that monitoring tools, sandboxes, and some security agents can fall into, indicating the script author anticipated running in a monitored or analyzed environment.

**Stage 5 - Environment Testing and Cleanup:** The script wrote and then deleted a test file (`/tmp/.testfile`) to confirm write access to the filesystem, followed by execution of the constructed payload scripts and a final cleanup pass.

Several commands in the sequence included plain-text comments, such as labeling the two `wget` methods as "Method 1" and "Method 2" respectively. This is notable from an attribution standpoint: professionally maintained malware and fully automated botnet tooling typically has such commentary stripped before deployment. Comments surviving into a live deployment session suggest the script was assembled from a public technique reference or tutorial without full sanitization, an operational security lapse on the part of whoever prepared this specific payload, even though the underlying technique itself (architecture fingerprinting, multi-method fallback delivery, anti-analysis process killing, basic obfuscation) reflects real, deliberate engineering rather than unsophisticated manual effort.

***

## Section 6: External Threat Intelligence Confirmation

The source IP for this session, 91.199.133.133, was checked against VirusTotal.

<figure><img src="../.gitbook/assets/image (447).png" alt=""><figcaption></figcaption></figure>

The IP was flagged as malicious by 19 of 91 security vendors. More significantly, VirusTotal's Communicating Files data independently confirms this exact attack chain: a file named `real_x86_64`, matching the architecture-specific binary name requested in the session's socket-based fallback method, and a file named `deploy.sh`, matching the exact filename requested in the session's wget-based methods, both appear in VirusTotal's records of files this IP has served, each with double-digit detection counts as malicious ELF binaries and shell scripts. This independently corroborates that the honeypot captured a real, active malware deployment infrastructure, not a one-off or isolated scripted probe.

***

## Section 7: Additional KQL Analysis

**Password reuse pattern**, to check for dictionary/wordlist-style attack behavior:

```kql
cowrie_CL
| where eventid == "cowrie.login.failed"
| summarize UsernamesTried = dcount(username) by password
| sort by UsernamesTried desc
| take 10
```

<figure><img src="../.gitbook/assets/image (448).png" alt=""><figcaption></figcaption></figure>

**Protocol breakdown**, comparing SSH versus Telnet activity volume:

```kql
cowrie_CL
| summarize count() by protocol
```

<figure><img src="../.gitbook/assets/image (449).png" alt=""><figcaption></figcaption></figure>

**Session duration outliers**, to identify unusually long sessions that might indicate manual/interactive attacker behavior rather than scripted activity:

```kql
cowrie_CL
| where eventid == "cowrie.session.closed"
| extend DurationSeconds = todouble(duration)
| sort by DurationSeconds desc
| take 10
| project src_ip, session, DurationSeconds
```

<figure><img src="../.gitbook/assets/image (450).png" alt=""><figcaption></figcaption></figure>

***

## Conclusion

Day 19 moved beyond routine KQL practice into a genuine finding: a fully automated malware deployment session, complete with architecture fingerprinting, redundant delivery fallbacks, basic obfuscation, and anti-analysis process killing, independently corroborated against VirusTotal through matching file names tied to the same source IP. This session alone provides strong material for MITRE ATT\&CK mapping in the days ahead, and demonstrates that despite this project's budget and infrastructure constraints, the Cowrie honeypot continues to capture data of real analytical value.
