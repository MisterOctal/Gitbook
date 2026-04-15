---
icon: chart-column
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1743519631241
coverY: 0
coverHeight: 138
---

# SOC Metrics and Objectives

**Date:** 07.04.2026

**Room Category:** Walkthrough

**Core Concept:** Measuring SOC efficiency through key indicators like MTTD, MTTA, and MTTR, and understanding how these metrics drive continuous improvement in threat detection and response.

In a SOC, metrics are not just numbers; they represent the team's ability to protect the organization. High-performing SOCs use these metrics to identify bottlenecks, reduce analyst burnout, and ensure that real threats are handled within agreed-upon timeframes.

***

## Task 1: Introduction

Efficiency in a SOC is measured using standardized indicators that track how quickly a team detects, acknowledges, and remediates threats.

**Learning Objectives:**

* Discover the concepts of SLA, MTTD, MTTA, and MTTR.
* Understand the impact of the False Positive Rate (FPR).
* Learn how L1 analysts can actively improve these metrics.
* Practice managing performance through simulated scenarios.

Question: Let's begin!

> **Answer:** No answer needed

***

## Task 2: Core Metrics

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/678ecc92c80aa206339f0f23/room-content/678ecc92c80aa206339f0f23-1746187204011.svg" alt="" width="375"><figcaption></figcaption></figure>

The reliability of a SOC team is measured by how accurately they filter noise to find real threats.

| Metric                          | Formula                          | Measures                             |
| ------------------------------- | -------------------------------- | ------------------------------------ |
| **Alerts Count (AC)**           | Total Count of Alerts Received   | Overall analyst workload.            |
| **False Positive Rate (FPR)**   | False Positives / Total Alerts   | Level of "noise" in detection rules. |
| **Alert Escalation Rate (AER)** | Escalated Alerts / Total Alerts  | Analyst independence/experience.     |
| **Threat Detection Rate (TDR)** | Detected Threats / Total Threats | Overall reliability (Must be 100%).  |

**Key Benchmarks:**

* **Alert Count:** Ideally 5–30 alerts per day per analyst.
* **FPR:** 80% or higher is a major issue; it leads to "alert fatigue" where analysts miss real threats due to boredom or over-saturation.
* **AER:** Ideally kept below 20%.

Question: Is zero alerts for one month a good sign for your SOC team? (Yea/Nay)

> **Answer:** Nay (Indicates broken visibility)

Question: What is the False Positive Rate if only 10 out of 50 alerts appear to be real threats?

> **Answer:** 80%

***

## Task 3: Triage Metrics (MTTx)

The timeline of a breach is measured by three primary "Mean Time" metrics, often dictated by a **Service Level Agreement (SLA)**.

<figure><img src="../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

1. **Mean Time to Detect (MTTD):** Time from the attack starting to the alert appearing in the SIEM (Benchmark: < 5 mins).
2. **Mean Time to Acknowledge (MTTA):** Time from the alert appearing to an analyst starting the triage (Benchmark: < 10 mins).
3. **Mean Time to Respond (MTTR):** Time from the start of triage to the threat being fully mitigated/contained (Benchmark: < 60 mins).

**Calculation Example:**

* **MTTD:** 12 minutes (Detection delay).
* **MTTA:** 10 minutes (Analyst delay).
* **MTTR:** 6 mins (triage) + 35 mins (remediation) = 41 minutes. (Total SOC response time = 10 + 41 = 51).

Question: If the team works 8/5, on which day of the week will they acknowledge a Saturday alert?

> **Answer:** Monday

Question: Provide the MTTD, MTTA, and MTTR for the malware scenario (12 min detect, 10 min ack, 6 min triage + 35 min L2 cleanup).

> **Answer:** 12,10,51

***

## Task 4: Improving Metrics

<figure><img src="../.gitbook/assets/image (24) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Metrics are used for performance evaluations and career growth. Improving them requires coordination across all SOC roles.

<table data-header-hidden><thead><tr><th width="140.39996337890625"></th><th></th></tr></thead><tbody><tr><td>Issue</td><td>Recommendation</td></tr><tr><td><strong>FPR > 80%</strong></td><td>Exclude trusted activity (updates) or use SOAR for auto-triage.</td></tr><tr><td><strong>MTTD > 30m</strong></td><td>Check log ingestion delays and optimize detection rule frequency.</td></tr><tr><td><strong>MTTA > 30m</strong></td><td>Distribute alerts evenly and use real-time notifications.</td></tr><tr><td><strong>MTTR > 4h</strong></td><td>Improve escalation speed and document response playbooks.</td></tr></tbody></table>

Question: What is the highest acceptable False Positive Rate for SOC teams?

> **Answer:** 80%

Question: Should all SOC roles work together to keep metrics improving? (Yea/Nay)

> **Answer:** Yea

***

## Task 5: Practice Scenarios

In a management simulation, improvements are assigned to fix specific metric failures.

Question: What flag did you get after completing the first scenario?

> **Answer:** THM{mttr:quick\_start\_but\_slow\_response}

Question: What flag did you get after completing the second scenario?

> **Answer:** THM{mttd:time\_between\_attack\_and\_alert}

Question: What flag did you get after completing the third scenario?

> **Answer:** THM{fpr:the\_main\_cause\_of\_l1\_burnout}

***

## Conclusion

Mastering SOC metrics allows an L1 analyst to move beyond simple triage into the realm of **SOC Engineering and Management**. By reducing the False Positive rate and improving the MTTR, analysts directly contribute to the organization's resilience against cyber threats.
