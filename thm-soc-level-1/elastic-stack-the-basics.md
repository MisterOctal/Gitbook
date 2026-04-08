---
icon: nfc-magnifying-glass
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5e8dd9a4a45e18443162feab/room-content/d43ff804dd897ce1b7394eb387e59302.png
coverY: 0
coverHeight: 138
---

# Elastic Stack: The Basics

**Date:** 08.04.2026

**Category:** Technical Analysis

**Core Concept:** This session focused on the Elastic Stack (ELK), an open-source collection of tools used for searching, analyzing, and visualizing large datasets. We analyzed how SOC teams leverage the stack as a SIEM-like solution to gain real-time visibility into endpoint telemetry and traffic.

The Elastic Stack provides a high-performance alternative to traditional SIEMs, allowing for massive scalability. By decoupling data collection (Beats) from processing (Logstash) and storage (Elasticsearch), it creates a flexible pipeline for rapid incident investigation.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (137).png" alt="" width="128"><figcaption></figcaption></figure>

The investigation began with an overview of the Elastic Stack (ELK), originally developed for log storage and search, now widely adopted for security operations. Organizations use it to monitor application performance and perform searches on large datasets, effectively functioning as a SIEM for many SOC teams.

Question: I am all set!

> **Answer:** No answer needed

***

## Task 2: Elastic Stack Overview

The Elastic Stack is composed of four main components that manage the lifecycle of a log from the endpoint to the analyst's dashboard.

#### Core Components

<figure><img src="../.gitbook/assets/image (138).png" alt="" width="563"><figcaption></figcaption></figure>

1. **Elasticsearch:** The "heart" of the stack. It is a full-text search and analytics engine that stores data in JSON format. It is highly scalable and supports a RESTful API for data interaction.
2. **Logstash:** A server-side data processing engine. It ingests data from multiple sources, applies **Filters** to normalize or enrich the data, and sends the **Output** to a destination (usually Elasticsearch).
3. **Beats:** Single-purpose, host-based agents (data shippers).
   * _Winlogbeat:_ Collects Windows Event Logs.
   * _Packetbeat:_ Monitors network traffic.
   * _Filebeat:_ Monitors system log files.
4. **Kibana:** The visualization layer. It provides the front-end interface where analysts perform searches, create charts, and build dashboards.

<figure><img src="../.gitbook/assets/image (139).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Logstash is used to visualize the data. (yay / nay)

> **Answer:** nay

Question: Elasticstash supports all data formats apart from JSON. (yay / nay)

> **Answer:** nay

***

## Task 3: Lab Connection

We established a connection to the ELK instance to perform a practical investigation of VPN telemetry. This setup simulates a SOC environment where logs are pre-indexed and ready for forensic analysis.

Question: Move to the next task!

> **Answer:** No answer needed

***

## Task 4: The Discover Tab

The Discover tab is where the SOC analysts spend most of their time. This tab shows the ingested logs, the search bar, normalized fields, and more. Analysts can search for the logs, investigate anomalies, and apply filters based on search terms and time periods.

<figure><img src="../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

* **1. Logs:** Each row shows a single log containing information about the event, along with the fields and values found in that log.
* **2. Fields Pane:** The left panel of the interface shows the list of fields parsed from the logs. We can click on any field to add it to the filter or remove it from the search.
* **3. Index Pattern:** Each type of log is stored in a different index pattern. We can select the index pattern from which we need the logs. For example, for VPN logs, we would need to select the index pattern in which VPN logs are stored.
* **4. Search Bar:** It is a place where the user adds search queries and applies filters to narrow down the results. In the next task, we will learn how to perform searches through queries.
* **5. Time Filter:** We can narrow down results based on any specific time duration.
* **6. Time Interval:** This chart shows the event counts over time.
* **7. TOP Bar:** This bar contains various options to save the search, open the saved searches, share or save the search, etc.
* **8. Discover Tab:** This is the main workspace in Kibana for exploring, searching, and analyzing raw data.
* **9. Add Filter:** We can apply filters to specific fields to narrow down results, rather than manually typing entire queries.

#### Investigation Findings (`vpn_connections`)

* **Timeframe:** Dec 31, 2021 – Feb 2, 2022.
* **Total Hits:** 2861.
* **Top IP:** `238.163.231.224`.
* **Top Traffic User:** James.

Question: Select the index vpn\_connections and filter from 31st December 2021 to 2nd Feb 2022. How many hits are returned?

<figure><img src="../.gitbook/assets/Screenshot 2026-04-08 065618.png" alt=""><figcaption></figcaption></figure>

> **Answer:** 2861

Question: Which IP address has the maximum number of connections?

<figure><img src="../.gitbook/assets/Screenshot 2026-04-08 065638.png" alt="" width="235"><figcaption></figcaption></figure>

> **Answer:** 238.163.231.224

Question: Which user is responsible for the overall maximum traffic?

<figure><img src="../.gitbook/assets/Screenshot 2026-04-08 065703.png" alt="" width="239"><figcaption></figcaption></figure>

> **Answer:** James

Question: Apply Filter on UserName Emanda; which SourceIP has max hits?

<figure><img src="../.gitbook/assets/Screenshot 2026-04-08 065727.png" alt=""><figcaption></figcaption></figure>

> **Answer:** 107.14.1.247

Question: On 11th Jan, which IP caused the spike observed in the time chart?

<figure><img src="../.gitbook/assets/Screenshot 2026-04-08 065854.png" alt=""><figcaption></figcaption></figure>

> **Answer:** 172.201.60.191

Question: How many connections were observed from IP 238.163.231.224, excluding the New York state?

<figure><img src="../.gitbook/assets/Screenshot 2026-04-08 070008.png" alt=""><figcaption></figcaption></figure>

> **Answer:** 48

***

## Task 5: KQL Overview

**KQL (Kibana Query Language)** is the syntax used to filter indexed documents. It is more intuitive than the legacy Lucene syntax and supports both free-text and field-based searches.

#### KQL Query Logic

* **Field-based Search:** `FieldName: "Value"` (e.g., `Source_Country: "United States"`).
* **Wildcards:** Use `*` to match partial strings (e.g., `United*`).
* **Logical Operators:**
  * **AND:** Requires both conditions to be true.
  * **OR:** Requires at least one condition to be true.
  * **NOT:** Excludes specific terms from results.

Question: Create a search query for Source\_Country: United States and User James or Albert. How many records? _Query used: `Source_Country: "United States" AND (UserName: "James" OR UserName: "Albert")`_

<figure><img src="../.gitbook/assets/Screenshot 2026-04-08 070602.png" alt=""><figcaption></figcaption></figure>

> **Answer:** 161

Question: Johny Brown was terminated Jan 1, 2022. How many VPN connections occurred after his termination? _Query used: `UserName: "Johny Brown"` with the time filter set to start from Dec 1, 2021._

<figure><img src="../.gitbook/assets/Screenshot 2026-04-08 070707.png" alt=""><figcaption></figcaption></figure>

> **Answer:** 1

***

## Task 6: Visualizations and Dashboards

We transitioned from raw data to visual intelligence by creating charts and dashboards. This is a critical step for SOC reporting and high-level monitoring.

* **Correlation:** By dragging fields into a visualization, we can see relationships, such as which `Source_IPs` are associated with which `Source_Countries`.
* **Failed Logins:** A table was created to track `Failed` status codes, allowing us to identify the users and IPs most frequently involved in unsuccessful connection attempts.

Question: Which user was observed with the greatest number of failed attempts?

> **Answer:** Simon

Question: How many wrong VPN connection attempts were observed in January?

> **Answer:** 274

***

## Task 7: Creating Dashboards

Dashboards provide consolidated visibility into log collections. By combining saved searches and visualizations, we created a custom dashboard to monitor VPN activity. This process involved selecting components from the library and arranging them to create a functional overview of the network's security posture.

<figure><img src="../.gitbook/assets/image (141).png" alt="" width="349"><figcaption></figcaption></figure>

Question: Create the dashboard containing the available visualizations.

> **Answer:** No answer needed

***

## Conclusion

The analysis confirmed that the Elastic Stack effectively handles the transition from raw logs to forensic clarity. By mastering KQL and the visualization engine, analysts can pinpoint specific malicious behaviors within massive datasets, providing a scalable foundation for real-time monitoring and incident response.
