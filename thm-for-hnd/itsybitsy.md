---
icon: magnifying-glass
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/62c435d1f4d84a005f5df811/room-content/74c341a0c330624314c2a3bc9137585a.png
coverY: 0
coverHeight: 139
---

# ItsyBitsy

**Date:** 16.05.2026&#x20;

**Room Category:** Challenge&#x20;

**Core Concept:** Utilizing Kibana (Elastic Stack) to investigate an IDS alert regarding potential Command and Control (C2) communication. This involves analyzing HTTP connection logs to identify the attacker's infrastructure, the native tools abused, and the hosted payload.

In this challenge, we act as a SOC analyst tasked with investigating a potential C2 communication from a user in the HR department. By filtering and analyzing week-long HTTP connection logs in Elastic, we must reconstruct the attacker's malware staging process.

***

## Task 1: Introduction

The objective is to access the Kibana interface and utilize the Discover tab to investigate the provided `connection_logs` index.

Question: Connect with the lab

> **Answer:** No answer needed

***

## Task 2: Scenario - Investigate a potential C2 communication alert

**Scenario:** During normal SOC monitoring, Analyst John observed an IDS alert indicating potential C2 communication from user Browne. A suspicious file containing a malicious pattern `THM:{ ________ }` was accessed. Due to limited resources, only week-long HTTP connection logs are available for the investigation.

Question: How many events were returned for the month of March 2022?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-16 070712.png" alt=""><figcaption></figcaption></figure>

> **Answer:** 1482

Question: What is the IP associated with the suspected user in the logs?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-16 070723.png" alt=""><figcaption></figcaption></figure>

_Note: 192.166.65.54 is the suspicious IP as there is only a small amount of traffic despite being meant to be a office workstation. This imitates the behavior of a potential C2._

> **Answer:** 192.166.65.54

Question: The user’s machine used a legit windows binary to download a file from the C2 server. What is the name of the binary?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-16 071031.png" alt=""><figcaption></figcaption></figure>

> **Answer:** bitsadmin

Question: The infected machine connected with a famous filesharing site in this period, which also acts as a C2 server used by the malware authors to communicate. What is the name of the filesharing site?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-16 071702.png" alt=""><figcaption></figcaption></figure>

> **Answer:** pastebin.com

Question: What is the full URL of the C2 to which the infected host is connected?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-16 071753.png" alt=""><figcaption></figcaption></figure>

> **Answer:** pastebin.com/yTg0Ah6a

Question: A file was accessed on the filesharing site. What is the name of the file accessed?

<figure><img src="../.gitbook/assets/Screenshot 2026-05-16 071828.png" alt=""><figcaption></figcaption></figure>

> **Answer:** secret.txt

Question: The file contains a secret code with the format THM{\_\_\_\_\_}.

> **Answer:** THM{SECRET\_\_CODE}
