---
icon: laptop-binary
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5e8dd9a4a45e18443162feab/room-content/a95f687a872a202a7db630a790b8a45f.png
coverY: 0
coverHeight: 137
---

# Benign

**Date:** 17.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Investigating host-centric logs in Splunk to identify suspicious process execution, living-off-the-land binaries (LOLBINs), and post-exploitation activity.

Security Information and Event Management (SIEM) tools are critical for analyzing endpoint telemetry. This challenge involves investigating a potentially compromised host using process execution logs. Identifying anomalous behavior requires correlating specific event IDs, analyzing command-line arguments, and filtering noise to pinpoint the exact sequence of an attack.

## Task 1: Introduction

This investigation focuses on host-centric logs to locate suspicious process execution. The primary dataset consists of Windows Event Logs, specifically Event ID 4688, which tracks new process creation. The logs required for this analysis are fully available within `win_eventlogs` index.

Question: Connect with the lab.

> **Answer:** No answer needed

***

## Task 2: Identify and Investigate an Infected Host

An Intrusion Detection System (IDS) flagged potentially suspicious process execution on a host within the HR department. Initial indicators suggest the execution of network information gathering tools and anomalous scheduled tasks.

Due to limited resources, the investigation relies solely on process execution logs (Event ID: 4688) ingested into the Splunk environment.

The network architecture is divided into three logical segments. Understanding this layout is helpful for identifying anomalies based on user roles and hostnames.

**IT Department:**

* James
* Moin
* Katrina

**HR Department:**

* Haroon
* Chris
* Diana

**Marketing Department:**

* Bell
* Amelia
* Deepak

Question: How many logs are ingested from the month of March, 2022?

Setting the time range in the Splunk interface to span the entire month of March 2022 and running a base search against the index reveals the total event count.

```bash
index="win_eventlogs"
```

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

> **Answer:** 13959

Question: Imposter Alert: There seems to be an imposter account observed in the logs, what is the name of that user?

Deduplicating the username field extracts a clean list of all accounts present in the environment. Reviewing this list against the known employee roster reveals a subtle spelling anomaly designed to blend in with normal traffic.

```bash
index="win_eventlogs"
| dedup UserName
| table UserName
```

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

> **Answer:** Amel1a

Question: Which user from the HR department was observed to be running scheduled tasks?

Filtering the search to include only HR department hostnames and the executable responsible for scheduled tasks (`schtasks.exe`) isolates the activity. Reviewing the resulting chart confirms aside from IT, only Chris has ran scheduled tasks.

```bash
index="win_eventlogs" HostName="HR*" schtasks.exe
```

<figure><img src="../.gitbook/assets/image (3).png" alt="" width="548"><figcaption></figcaption></figure>

> **Answer:** Chris.fort

Question: Which user from the HR department executed a system process (LOLBIN) to download a payload from a file-sharing host.

Searching the index for known Living Off The Land Binaries (LOLBINs) often used for downloading files from external sources points to a specific executable. Examining the events associated with this binary identifies the compromised user account.

```bash
index="win_eventlogs" certutil.exe
```

<figure><img src="../.gitbook/assets/image (4).png" alt="" width="563"><figcaption></figcaption></figure>

> **Answer:** haroon

Question: To bypass the security controls, which system process (lolbin) was used to download a payload from the internet?

The previous query isolates the specific legitimate system binary abused by the attacker to retrieve the payload.

> **Answer:** certutil.exe

Question: What was the date that this binary was executed by the infected host? format (YYYY-MM-DD)

The exact execution date is recorded within the timestamp of the event log detailing the download command.

> **Answer:** 2022-03-04

Question: Which third-party site was accessed to download the malicious payload?

The command-line arguments captured in Event ID 4688 show the full URL, which exposes the specific external file-sharing domain utilized by the attacker.

> **Answer:** controlc.com

Question: What is the name of the file that was saved on the host machine from the C2 server during the post-exploitation phase?

The command-line execution logs document the destination output flag, which reveals the exact name given to the downloaded executable.

> **Answer:** benign.exe

Question: The suspicious file downloaded from the C2 server contained malicious content with the pattern THM{..........}; what is that pattern?

Navigating directly to the external URL discovered in the command-line logs reveals the hosted text file and the required pattern payload.

<figure><img src="../.gitbook/assets/image (1).png" alt="" width="369"><figcaption></figcaption></figure>

> **Answer:** THM{KJ&\*H^B0}

Question: What is the URL that the infected host connected to?

The entire connection string is visible within the arguments of the executed download command.

> **Answer:** https://controlc.com/e4d11035
