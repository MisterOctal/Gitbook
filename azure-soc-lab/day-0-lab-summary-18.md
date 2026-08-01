---
icon: chart-waterfall
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 18 - Cowrie + KQL2

**Date:** 01.08.2026

**Core Concept**: Custom Log Ingestion Pipeline Engineering and Troubleshooting

Today's goal was building a working pipeline to get Cowrie's JSON logs into Microsoft Sentinel for KQL analysis, replacing the retired ELK stack as the analytical layer for honeypot data. What was expected to be a straightforward custom log connector setup turned into a genuine troubleshooting exercise across region restrictions, resource provider gaps, authentication failures, and a file-tailing limitation that ultimately required accepting a real data limitation rather than chasing a perfect fix. This writeup documents the full process, including the dead ends, since the troubleshooting itself is as much a demonstration of SOC-relevant skill as the final result.

***

## Section 1: Overview

The plan was simple in concept: install the Azure Monitor Agent (AMA) on the Cowrie honeypot VM, configure a Data Collection Rule (DCR) to tail the Cowrie JSON log file, map it into a custom Log Analytics table, and query it with KQL. In practice, this required working through a resource provider gap specific to the VM's region, a Managed Identity authentication failure, and a Fluent Bit file-tailing limitation that ultimately meant historical timestamp preservation had to be abandoned as out of scope for this stage of the project.

<figure><img src="../.gitbook/assets/image (440).png" alt="" width="563"><figcaption></figcaption></figure>

***

## Section 2: Table and Schema Setup

A custom table (`cowrie_CL`) was created in the Log Analytics Workspace to receive Cowrie event data. Rather than guess at a schema, a real sample was pulled directly from the honeypot's live log file, covering multiple distinct Cowrie event types (`cowrie.login.success`, `cowrie.login.failed`, `cowrie.session.connect`, `cowrie.session.closed`, `cowrie.client.kex`, `cowrie.client.version`, `cowrie.command.input`) to ensure the schema could accommodate the full variety of fields Cowrie actually produces, not just one event type's shape.

The following schema was defined based on the real sample:

| Column        | Type     |
| ------------- | -------- |
| TimeGenerated | datetime |
| eventid       | string   |
| src\_ip       | string   |
| src\_port     | int      |
| dst\_ip       | string   |
| dst\_port     | int      |
| session       | string   |
| protocol      | string   |
| username      | string   |
| password      | string   |
| input         | string   |
| duration      | real     |
| version       | string   |
| hassh         | string   |
| message       | string   |
| sensor        | string   |
| uuid          | string   |

The associated DCR transformation:

```kql
source
| extend TimeGenerated = todatetime(timestamp)
| extend duration = todouble(duration)
| project
    TimeGenerated,
    eventid,
    src_ip,
    src_port = toint(src_port),
    dst_ip,
    dst_port = toint(dst_port),
    session,
    protocol,
    username,
    password,
    input,
    duration,
    version,
    hassh,
    message,
    sensor,
    uuid
```

***

## Section 3: Error 1 - Resource Provider Not Registered in Region

The first real blocker came when associating the DCR with the honeypot VM. The Portal returned an error stating no registered resource provider existed for `dataCollectionRuleAssociations` in the VM's region (Malaysia West) for any supported API version, even though the region itself is otherwise valid and allowed under this subscription.

This is distinct from earlier region-restriction issues encountered elsewhere in the project. It isn't a subscription policy limiting allowed regions, it's a case of a specific resource type simply not being provisioned in that region at all. The Portal UI offers no way to override or work around this for the association step specifically, since it inherits the VM's region automatically with no separate region selector.

**Resolution:** the association was created successfully via PowerShell in Azure Cloud Shell instead of through the Portal UI, using the `New-AzDataCollectionRuleAssociation` cmdlet. This bypassed whatever check was blocking the Portal-driven flow, while the DCR and destination table themselves remained correctly hosted in Japan East, matching the rest of the Sentinel setup from Day 16.

***

## Section 4: Error 2 - Managed Identity / MSI Authentication Failure

After installing the AMA extension and creating the association, the agent's error log showed repeated authentication failures:

```
Failed to get MSI token from IMDS endpoint: http://169.254.169.254 ErrorCode:-2146041343
```

This meant the agent could not authenticate to Azure at all, since it had no way to retrieve a token from the instance metadata service. The root cause was that System Assigned Managed Identity had never been enabled on the honeypot VM, a step that's easy to overlook since it's a separate configuration screen from anything directly related to monitoring or logging.

**Resolution:** System Assigned Managed Identity was enabled on the VM under its Identity settings, followed by a manual restart of the `azuremonitoragent` service to force it to re-attempt token acquisition. The error stopped appearing in the logs immediately after the restart, and a valid ingestion token was successfully retrieved shortly after.

***

## Section 5: Error 3 - Fluent Bit Not Detecting File Changes

With authentication resolved, the pipeline still showed zero data landing in `cowrie_CL`, despite the DCR configuration, table schema, and file path all being correctly set up and confirmed present in the agent's local config cache. Manually appending test data to the Cowrie log file, both from the archived historical files and a freshly written test line, produced no reaction whatsoever in Fluent Bit's own runtime log, even in real time.

Investigation confirmed this is a known limitation: Fluent Bit's tail input, as configured by AMA on Linux, is not reliably reacting to file growth through polling alone when the `Inotify_Watcher` option is set to false, which is the default in AMA's generated configuration. Since this configuration file is auto-generated by AMA from the DCR definition, it isn't intended to be hand-edited directly, and the Portal doesn't expose this setting for adjustment.

**Workaround identified:** restarting the `azuremonitoragent` service forces Fluent Bit to reinitialize and read the current state of the file fresh, which does successfully pick up all content present in the file at that moment. This isn't true continuous live tailing, but it is a reliable, repeatable way to get data ingested on demand.

***

## Section 6: Limitation - Historical Timestamps Not Preserved

Once the restart workaround was discovered, all previously appended historical log data (spanning archive files from May through late July) ingested successfully in a single batch, roughly 46,000 events. However, checking the actual timestamp distribution revealed every single event was stamped with today's date, rather than the real historical dates embedded in the original JSON.

Investigation showed the `timestamp` field was present and correctly typed in the ingested data, but its value matched `TimeGenerated` exactly on every row, meaning the original embedded event time was not preserved through the ingestion pipeline, most likely overwritten somewhere in AMA's JSON parsing stage before the DCR's transformation logic ever had a chance to run against the original value.

**This was accepted as a known limitation rather than pursued further.** Reconstructing accurate historical timestamps at this point would require deleting and re-ingesting the full dataset with a more carefully tested pipeline, which was judged not to be a reasonable use of the remaining project budget and timeline, given the actual event content, source IPs, commands, and session data are all still fully intact and usable. Historical per-day trend analysis on the backfilled portion of this dataset simply isn't reliable, but the dataset overall remains valid for content-based analysis (top commands, credential patterns, source IP behavior, MITRE mapping).

Going forward, any newly ingested data via a fresh restart will be close enough to real time to be treated as reliable, since it reflects genuinely current activity rather than backfilled history.

\[SCREENSHOT HERE - KQL query result showing TimeGenerated and timestamp columns matching, illustrating the limitation directly]

***

## Section 7: Verification and Data Quality Check

Once ingestion was confirmed working, the event type distribution was checked to confirm the data was genuine and not an artifact of a parsing failure:

```kql
cowrie_CL
| summarize count() by eventid
| sort by count_ desc
```

<figure><img src="../.gitbook/assets/image (438).png" alt=""><figcaption></figcaption></figure>

The results showed a realistic spread consistent with actual honeypot activity: roughly 10,246 session connect and session close events each, 9,587 client version fingerprints, 8,909 key exchange events, 7,609 failed logins against only 40 successful logins, and 203 executed commands. This distribution, with failures dramatically outnumbering successes and a meaningful but proportionally small number of actual post-exploitation command executions, matches expected honeypot behavior and confirms the data itself is trustworthy even with the timestamp limitation noted above.

***

## Section 8: Cowrie KQL Practice

With the pipeline confirmed and verified, a few introductory queries were run to begin exploring the dataset ahead of deeper analysis planned for the following day.

**Top source IPs by failed login attempts:**

```kql
cowrie_CL
| where eventid == "cowrie.login.failed"
| summarize FailedAttempts = count() by src_ip
| sort by FailedAttempts desc
| take 10
```

<figure><img src="../.gitbook/assets/Screenshot 2026-08-01 161443.png" alt=""><figcaption></figcaption></figure>

**Most commonly attempted username/password pairs:**

```kql
cowrie_CL
| where eventid == "cowrie.login.failed"
| summarize count() by username, password
| sort by count_ desc
| take 10
```

<figure><img src="../.gitbook/assets/Screenshot 2026-08-01 161507.png" alt=""><figcaption></figcaption></figure>

**Successful logins with associated session IDs, for follow-up command analysis:**

```kql
cowrie_CL
| where eventid == "cowrie.login.success"
| project TimeGenerated, username, password, src_ip, session
```

<figure><img src="../.gitbook/assets/Screenshot 2026-08-01 161545.png" alt=""><figcaption></figcaption></figure>

**Commands entered during sessions:**

```kql
cowrie_CL
| where eventid == "cowrie.command.input"
| project TimeGenerated, src_ip, session, input
| sort by TimeGenerated desc
```

<figure><img src="../.gitbook/assets/Screenshot 2026-08-01 161624.png" alt=""><figcaption></figcaption></figure>

***

## Conclusion

Day 18 delivered a working Cowrie-to-Sentinel pipeline, but not without a genuine, multi-layered troubleshooting process: a regional resource provider gap requiring a CLI-based workaround, a missed Managed Identity configuration step, and a file-tailing limitation in AMA's Fluent Bit implementation that ultimately couldn't be fully resolved within a reasonable time and cost budget. Rather than treat the timestamp limitation as a failure, it's being documented plainly as a real constraint of this setup, one that doesn't compromise the usability of the dataset for content-based analysis, detection engineering, or MITRE mapping, which is where the remaining project days are focused. Given the budget and time constraints established earlier this week, accepting a well-understood limitation and moving forward with genuinely valid data was the right call over spending further resources chasing a fully clean historical rebuild.
