---
icon: chart-bar
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 16 - KQL 1

**Date:** 20.07.2026

**Core Concept**: KQL Query Practice and External Threat Confirmation

Today's focus was on getting hands-on KQL practice against the Windows honeypot's log data in Log Analytics, along with objectively confirming the top brute-forcing IP against an external threat intelligence source.

***

## Section 1: Overview

With Log Analytics accessible directly, several KQL queries were run against the `Event` table to break down failed logon attempts by source IP, successful logon attempts filtered to the decoy accounts, and attack volume over time. One flagged successful logon on a decoy account was also cross-checked and confirmed to be a service logon rather than a genuine compromise.

***

## Section 2: LAW and Sentinel Setup

A Log Analytics Workspace was created to start building out Sentinel/SIEM experience alongside the existing ELK stack. The workspace ended up provisioned in Japan East, since the subscription's allowed region list didn't include the originally planned region. Microsoft Sentinel was then enabled on top of the workspace.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-19 120738.png" alt=""><figcaption></figcaption></figure>

Some difficulties were encountered getting the Windows Security Events connector fully configured through the Defender portal, so log queries for this session were run directly against the workspace's `Event` table in Log Analytics rather than through Sentinel's own interface. This didn't affect the ability to query and analyze the honeypot's security log data.

***

## Section 3: Failed Logon Attempts by Source IP

```kql
Event
| where EventLog == "Security" and EventID == 4625
| extend IpAddress = extract(@"<Data Name=['""]IpAddress['""]>([^<]+)</Data>", 1, EventData)
| summarize FailedAttempts = count() by IpAddress
| sort by FailedAttempts desc
```

<figure><img src="../.gitbook/assets/Screenshot 2026-07-20 131317.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-07-19 134535.png" alt=""><figcaption></figcaption></figure>

This returned 12 distinct source IPs over the last 48 hours, with counts ranging from 2 to 11 failed attempts each. The top result, 177.8.140.154 with 11 failed attempts, was checked against AbuseIPDB and objectively confirmed as malicious, with a 100% abuse confidence score and 105 total reports, tied to a Brazilian ISP (turbo telecom provedor de acesso a internet LTDA). This is the same IP responsible for the largest share of brute force volume in this dataset, at 21.15% of all failed attempts.

***

## Section 4: Failed Logon Attempts by Username

```kql
Event
| where EventLog == "Security" and EventID == 4625
| extend TargetUserName = extract(@"<Data Name=['""]TargetUserName['""]>([^<]+)</Data>", 1, EventData)
| summarize count() by TargetUserName
```

<figure><img src="../.gitbook/assets/Screenshot 2026-07-20 131134.png" alt=""><figcaption></figcaption></figure>

Over the 48 hour window, Administrator accounted for 90.38% of failed logon attempts, with the remaining 9.62% falling under NOUSER (a placeholder logged when Windows can't resolve an attempted username to an existing account). This confirms Administrator remains the overwhelmingly dominant target even at this smaller sample size.

***

## Section 5: Attack Volume Over Time

```kql
Event
| where EventLog == "Security" and EventID == 4625
| summarize AttemptCount = count() by bin(TimeGenerated, 1h)
| sort by TimeGenerated asc
```

<figure><img src="../.gitbook/assets/Screenshot 2026-07-20 131212 (1).png" alt=""><figcaption></figcaption></figure>

Attack volume was bucketed hourly, showing 23 attempts in one hour and 29 in the next, confirming brute force activity is ongoing and consistent rather than a single isolated burst.

***

## Section 6: Decoy Account Successful Logon Check

```kql
Event
| where EventLog == "Security" and EventID == 4624
| extend TargetUserName = extract(@"<Data Name=['""]TargetUserName['""]>([^<]+)</Data>", 1, EventData)
| where TargetUserName in ("admin", "guest", "test", "backup", "support", "sql", "user", "demo")
```

<figure><img src="../.gitbook/assets/Screenshot 2026-07-20 131410.png" alt=""><figcaption></figcaption></figure>

This query returned successful logon events against the "user" and "admin" decoy accounts. These were investigated further before drawing any conclusions, since a successful logon alone doesn't confirm an external compromise. After checking the associated Logon Type and source IP for these events, they were confirmed to be service logons rather than genuine external access, consistent with expected platform/service account behavior rather than an actual brute force success.

***

## Conclusion

Day 16 provided solid KQL reps against real honeypot data, covering failed logon aggregation, time-based volume analysis, and decoy account monitoring. The top attacking IP was objectively confirmed as malicious via AbuseIPDB rather than assumed, and the flagged decoy account logons were properly investigated and ruled out as service activity rather than treated as a false compromise.
