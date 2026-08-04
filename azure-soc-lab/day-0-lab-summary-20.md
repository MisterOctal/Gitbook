---
icon: shield
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 20 - Workbooks Analytics

**Date:** 04.08.2026

**Core Concept:** Sentinel Workbooks and Custom Analytics Rules

Following the deeper KQL analysis and the discovery of the multi-stage malware deployment chain over the past couple of days, today's focus shifted towards operationalizing that data within Microsoft Sentinel. The objective was to transition from ad-hoc queries to foundational visualizations and automated alerting by constructing a basic workbook and deploying test analytics rules based on patterns observed in the Cowrie honeypot dataset.

***

## Section 1: Overview

The day's activities were divided into two main tasks: constructing a high-level overview workbook in Sentinel to visualize key metrics from the ingested logs, and translating previously developed KQL queries into scheduled analytics rules to validate the alerting pipeline. This represents a structural step in converting raw honeypot data into actionable security monitoring, establishing a baseline for automated analysis.

***

## Section 2: Building the Cowrie Overview Workbook

A new workbook was created in Sentinel to establish a consolidated view of honeypot activity. The primary goal was to enable rapid visualization of attacker volume, source origins, and common interaction trends without requiring manual query execution. The workbook relies on four foundational KQL queries to populate its visualizations.

The top section of the workbook highlights attack volume and origin. It features a bar chart detailing the top source IPs interacting with the honeypot, driven by the following query:

<figure><img src="../.gitbook/assets/Screenshot 2026-08-04 190835.png" alt=""><figcaption></figcaption></figure>

```kql
cowrie_CL
| where eventid == "cowrie.login.failed"
| summarize FailedAttempts = count() by src_ip
| sort by FailedAttempts desc
| take 10
```

A supplementary donut chart illustrates protocol distribution, confirming that SSH traffic overwhelmingly dominates Telnet. This is populated by a basic summarization query:

```kql
cowrie_CL
| summarize count() by protocol
```

The lower section of the workbook provides visibility into attack characteristics. A bar chart tracks password reuse patterns, visualizing which passwords were attempted most frequently across different usernames, using this query:

<figure><img src="../.gitbook/assets/Screenshot 2026-08-04 190855.png" alt=""><figcaption></figcaption></figure>

```kql
cowrie_CL
| where eventid == "cowrie.login.failed"
| summarize UsernamesTried = dcount(username) by password
| sort by UsernamesTried desc
| take 10
```

An additional donut chart displays the distribution of Cowrie event types, serving as a functional overview of the interaction types most frequently recorded by the honeypot:

```kql
cowrie_CL
| summarize count() by eventid
| sort by count_ desc
```

***

## Section 3: Creating Scheduled Analytics Rules

Following the establishment of visual dashboards, the focus moved to configuring automated alerting. Scheduled analytics rules were implemented based on behavioral patterns previously identified during manual dataset review.

The first rule was constructed to detect the multi-stage deployment activity observed in earlier analysis. The query logic identifies specific command inputs that combine download utilities (`wget`, `curl`), HTTP references, and common execution or temporary directory targets (`.sh`, `/tmp/`).

<figure><img src="../.gitbook/assets/Screenshot 2026-08-04 184523 (1).png" alt=""><figcaption></figcaption></figure>

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| where input has_any ("wget", "curl") and input has "http"
| where input has_any (".sh", "/tmp/")
```

This rule was assigned a High severity rating and mapped to the Command and Control (T1105) and Execution tactics. This specific syntax combination strongly indicates an active attempt to retrieve and execute a payload, distinct from basic reconnaissance or credential brute-forcing.

A secondary, Low severity rule was implemented to monitor successful logins. This query establishes a baseline access-confirmation signal, catching any successful authentication regardless of subsequent attacker behavior.

<figure><img src="../.gitbook/assets/Screenshot 2026-08-04 184957.png" alt=""><figcaption></figcaption></figure>

```kql
cowrie_CL
| where eventid == "cowrie.login.success"
```

This rule was mapped to the Initial Access tactic. While successful authentication is the expected outcome for a honeypot designed for observation, tracking this event remains necessary for correlating initial access with subsequent post-exploitation activity.

***

## Section 4: Reviewing Active Rules

After configuration, the rules were verified as active and operational within the Sentinel environment.

<figure><img src="../.gitbook/assets/Screenshot 2026-08-04 190713.png" alt=""><figcaption></figcaption></figure>

The active rules list confirms the deployment of both the "Cowrie - Login Successful" and "Cowrie - Fetch and Execute Pattern" rules. This configuration ensures that if activity matching previously analyzed patterns reoccurs, Sentinel will automatically generate a corresponding incident, translating raw log ingestion into actionable security monitoring.

***

## Conclusion

Day 20 focused on practical implementation within the Sentinel environment. The creation of a foundational workbook and the deployment of initial analytics rules established the framework for automated monitoring of the Cowrie data stream. The visual dashboards deliver a concise summary of the attack landscape, while the KQL-driven alerting rules provide a basis for proactive detection, setting the stage for more complex correlation and threat hunting in subsequent phases.
