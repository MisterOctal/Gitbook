---
icon: inbox-full
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/5fc2847e1bbebc03aa89fbf2-1755612149483
coverY: 0
coverHeight: 138
---

# Invite Only

**Date:** 11.05.2026&#x20;

**Room Category:** Challenge&#x20;

**Core Concept:** Extracting insight from a set of flagged artifacts (IPs and hashes) and distilling the information into usable threat intelligence through extensive OSINT and malware sandbox analysis.

In this scenario, a Tier 1 SOC analyst at TrySecureMe escalated two suspicious findings (an IP address and a file hash) discovered early in the morning. The objective is to act as a supporting analyst to a Tier 3 investigator, analyzing these artifacts to reconstruct the attack chain and attribute the activity to a specific threat report.

***

## Task 1: Invite Only

The investigation centers around two specific Indicators of Compromise (IOCs):

* **Flagged IP:** `101[.]99[.]76[.]120`
* **Flagged SHA256 hash:** `5d0509f68a9b7c415a726be75a078180e3f02e59866f193b0a99eee8e39c874f`

**Analysis Methodology:** While the room environment provides an internal threat intelligence search application (`TryDetectThis2.0`), this challenge was successfully completed by extensively leveraging **VirusTotal**. Every portion of the VirusTotal platform was utilized to gather context:

* **Details & Behavior Tabs:** Used to identify the exact file type, associated names, dropped files, and execution parent processes.
* **Relations Tab:** Used to map out the infrastructure, identify execution parents, and uncover the sequence of dropped files.
* **Community Comments:** Provided crucial context and pivot points that helped identify the overarching malware family and the specific phishing techniques used.
* **Google Intergration:** Used to search for the exact indicators and community keywords to locate the original, comprehensive threat intelligence report detailing the attack campaign.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Question: What is the name of the file identified with the flagged SHA256 hash?

> **Answer:** syshelpers.exe

Question: What is the file type associated with the flagged SHA256 hash?

> **Answer:** Win32 EXE

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" width="512"><figcaption></figcaption></figure>

Question: What are the execution parents of the flagged hash? List the names chronologically, using a comma as a separator. Note down the hashes for later use.

> **Answer:** 361GJX7J,installer.exe

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: What is the name of the file being dropped? Note down the hash value for later use.

> **Answer:** Aclient.exe

<figure><img src="../.gitbook/assets/image (3) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Research the second hash in question 3 and list the four malicious dropped files in the order they appear (from up to down), separated by commas.

> **Answer:** searchhost.exe,syshelpers.exe,nat.vbs,runsys.vbs

<figure><img src="../.gitbook/assets/image (4) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Analyse the files related to the flagged IP. What is the malware family that links these files?

> **Answer:** asyncrat

Question: What is the title of the original report where these flagged indicators are mentioned? Use Google to find the report.

> **Answer:** From Trust to Threat: Hijacked Discord Invites Used for Multi-Stage Malware Delivery

For the last few tasks, we can simply read the article to extract the answers.

Question: Which tool did the attackers use to steal cookies from the Google Chrome browser?

> **Answer:** ChromeKatz

Question: Which phishing technique did the attackers use? Use the report to answer the question.

> **Answer:** ClickFix

Question: What is the name of the platform that was used to redirect a user to malicious servers?

> **Answer:** Discord
