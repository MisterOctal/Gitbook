---
icon: windows
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 13 - Windows Analys I

**Date:** 09.07.2026

**Core Concept**: RDP Brute Force Pattern Analysis and Deception Failure Review

A clear picture of automated RDP brute forcing is starting to form against the Windows honeypot, along with a few notable gaps and one concerning discovery about hostname exposure. Sadly I had to go somewhere so I had to rush today's findings, not many screenshots today.

***

## Section 1: Overview

This round of analysis is based on 166 recorded login attempts against the Windows honeypot. The data reveals a mix of automated dictionary-based scanning, a known malicious IP with a honeypot-hitting history, and an unexpected hostname leak, all despite the honeypot not yet having a weak password configured.

***

## Section 2: Top Attacking Source IPs

<figure><img src="../.gitbook/assets/Screenshot 2026-07-08 142842.png" alt="" width="300"><figcaption></figcaption></figure>

The most aggressive source IP is 45.142.193.166, responsible for 14.46% of all attempts. A VirusTotal check confirms this IP is already flagged as malicious and has a known history of hitting honeypot infrastructure specifically, meaning this isn't an isolated attacker but part of a broader scanning operation that has been caught before.

The next most active IPs round out the top contributors:

* 34.123.42.157 - 9.04%
* 74.234.195.2 - 7.23%
* 109.75.161.149 - 6.02%
* 20.79.166.214 - 6.02%
* 212.132.118.184 - 4.82%
* 3.93.176.159 - 4.82%
* 160.25.28.205 - 4.22%
* 202.83.120.49 - 4.22%

The long tail is significant, with "Other" accounting for nearly 39.16% of traffic, indicating a wide spread of low-volume scanning sources rather than a single dominant botnet.

***

## Section 3: Username Targeting Patterns

<figure><img src="../.gitbook/assets/Screenshot 2026-07-08 143233.png" alt="" width="225"><figcaption></figcaption></figure>

The targeting is heavily weighted toward generic admin accounts. Administrator (12.7%) and its lowercase and localized variants collectively make up over a third of all attempts:

* Administrator - 12.7%
* administrator - 8.4%
* admin - 7.2%
* Administrador - 6.6%
* Admin - 4.8%
* Guest - 3.0%
* administrador - 3.0%
* Test - 2.4%
* Alix.Yeargain - 1.2%

This confirms attackers are running standard dictionary lists rather than anything targeted. Alix.Yeargain isn't the only human name in the dataset either, several other individual human names appear in the low-volume "Other" bucket as well. This pattern is a bit more concerning than generic dictionary spraying, since real human names suggest attackers may be working from leaked credential dumps or breach compilations rather than purely generic wordlists, which would mean this VM is being targeted with at least some pre-existing intelligence rather than blind brute forcing alone.

***

## Section 4: Deception Configuration Mistake

An important operational mistake surfaced this round: the VM was never configured with a weak password. As a result, none of the brute force attempts have succeeded, and Sysmon has captured zero post-exploitation activity. What we have instead is a clean dataset of failed authentication attempts only. This limits the depth of analysis for now but still gives good visibility into pre-exploitation behavior, including targeting patterns, source IPs, and timing.

***

## Section 5: Hostname Leak

The most notable finding this round is a hostname leak. The username vm-windows-hone appears in the target list at 6.0%, which is a truncated version of this VM's actual hostname, vm-windows-honeypot. This means the hostname was exposed to attackers before any successful login occurred, likely through the RDP negotiation process itself rather than through any post-exploitation activity. This is a meaningful finding on its own, since it demonstrates that metadata leakage can happen independent of successful compromise.

***

## Conclusion

Day 13 highlights both a data collection gap and a genuine security finding. The missing weak password means the Windows honeypot has not yet captured any post-exploitation behavior, but the failed attempts alone confirm active, sustained scanning from known malicious infrastructure, along with an unexpected hostname disclosure through the RDP protocol itself.
