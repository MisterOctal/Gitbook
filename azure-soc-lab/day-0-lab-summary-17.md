---
icon: power-off
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 17 - Trimming/Reflection

**Date:** 29.07.2026

**Core Concept**: Infrastructure Decommissioning and Budget Governance

With the remaining Azure credit down to $25 and burning at roughly $1.50/day even with VMs stopped, today's focus was on cutting costs by decommissioning infrastructure that had already served its purpose, and refocusing the final stretch of the project on Cowrie.

***

## Section 1: Why the Windows Honeypot Was Decommissioned

The Windows honeypot had already delivered its core value: RDP brute force telemetry, username targeting patterns, geographic distribution via Kibana, and a full round of KQL practice against both the `Event` and would-be `SecurityEvent` tables in Log Analytics. Beyond that, the VM had hit a hard ceiling. Azure's platform-level hardening consistently intervened on any successful decoy account logon, meaning no genuine post-exploitation activity was ever captured, regardless of how weak the account passwords were set. Continuing to run the VM meant paying for compute with no realistic path to new data. Given the budget constraint, it was deleted entirely rather than left stopped, since stopped VMs still incur disk storage costs.

***

## Section 2: Why the ELK Stack Was Decommissioned

The ELK VM had also already delivered its intended value: Kibana dashboards, the Day 15 geo-mapped attack visualization, and hands-on Filebeat/Winlogbeat pipeline experience. Maintaining Elasticsearch, Logstash, and Kibana going forward added ongoing compute cost for a stack that had already been fully exercised, with Sentinel and Log Analytics now covering the same analytical role going forward at a fraction of the cost. Rather than keep paying to run infrastructure whose learning value was already banked, the ELK VM was decommissioned as well.

***

## Section 3: Cowrie as the Remaining Focus

With both of the above removed, Cowrie is now the sole active honeypot for the remainder of the project. Unlike the Windows VM, Cowrie has consistently produced genuine attacker interaction, including successful logins, command execution, and full attack chains, without running into the platform-level interference that blocked meaningful data on the Windows side. Cowrie was simply powered back on to resume data collection.

***

## Section 4: Honest Retrospective on Budget and Architecture

Looking back, a fully cloud-hosted ELK stack likely wasn't the most budget-efficient choice for this scale of project. A hybrid approach, running ELK and Uptime Kuma locally while keeping only the honeypot VMs themselves in the cloud, would have preserved the same learning experience and log pipeline practice while cutting cloud compute costs significantly, since ELK's own resource footprint was a larger ongoing expense than the honeypots it was analyzing.

<figure><img src="../.gitbook/assets/image (436).png" alt=""><figcaption></figcaption></figure>

There's also a personal factor worth being upfront about. Budget could have stretched further with tighter time management. Gaps between lab days, where attention shifted to coursework and other projects, meant VMs sometimes sat running or stopped (and billing for storage) longer than necessary between active work sessions. A more consistent day-to-day cadence would have made the $100 credit go noticeably further.

***

## Conclusion

Day 17 trims the lab down to what's actually still producing value. Windows and ELK are gone, both having already delivered their core lessons, and Cowrie remains as the one honeypot still capable of generating new, genuine attack data for the final stretch of the project.
