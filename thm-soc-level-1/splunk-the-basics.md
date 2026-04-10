---
icon: nfc-magnifying-glass
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5e8dd9a4a45e18443162feab/room-content/a95f687a872a202a7db630a790b8a45f.png
coverY: 0
coverHeight: 138
---

# Splunk: The Basics

**Date:** 08.04.2026

**Room Category:** Walkthrough

**Core Concept:** This session focused on the Splunk SIEM architecture and its log ingestion pipeline. We looked at how machine data is converted into searchable events and applied Search Processing Language (SPL) to filter through VPN telemetry for specific security indicators.

Splunk acts as the central hub for security operations, allowing us to correlate data from all over the network in one place. This visibility is what makes it possible for analysts to catch threats early and reduce the overall time a transition or attack goes unnoticed.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

We started by looking at how Splunk takes a mess of raw data and organizes it into a single, searchable interface. This is essential for modern security work, as it gives us a clear window into network activity that would otherwise be buried in thousands of separate log files.

Question: Continue with the next task.

> **Answer:** No answer needed

***

## Task 2: Connect with the Lab

After connecting to the standalone Splunk instance, we had the environment needed to simulate a real-world scenario. This allowed us to practice moving data through the pipeline and running queries against a live dataset.

Question: Connect with the lab.

> **Answer:** No answer needed

***

## Task 3: Splunk Components

Splunk's architecture relies on three parts to get data from a source into an analyst's search results:

#### 1. Splunk Forwarder

<figure><img src="../.gitbook/assets/image (131).png" alt="" width="375"><figcaption></figcaption></figure>

The **Forwarder** is the entry point. It’s a small agent installed on servers or workstations that grabs logs and sends them off to the indexer. It’s designed to be very light on resources so it doesn't slow down the machines it's monitoring.

#### 2. Splunk Indexer

<figure><img src="../.gitbook/assets/image (132).png" alt="" width="188"><figcaption></figcaption></figure>

The **Indexer** is where the heavy lifting happens. It takes the raw data strings, breaks them down into useful fields (like IPs or timestamps), and stores them in "indexes." This organization is what makes searching through millions of logs so fast.

#### 3. Search Head

<figure><img src="../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

The **Search Head** is what the analyst actually sees. It’s the web interface where we write SPL queries, build out dashboards, and create the charts and tables used for reporting.

Question: Which component is used to collect and send data over the Splunk instance?

> **Answer:** Forwarder

***

## Task 4: Navigating Splunk

The interface is built to help you jump quickly between management and active investigation:

<figure><img src="../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

* **Splunk Bar:** Your global control center for system alerts, settings, and tracking active search jobs.

<figure><img src="../.gitbook/assets/image (135).png" alt="" width="204"><figcaption></figcaption></figure>

* **Apps Panel:** Where the functional modules live, specifically **Search & Reporting**, which is the primary workspace for investigating incidents.

<figure><img src="../.gitbook/assets/image (136).png" alt="" width="563"><figcaption></figcaption></figure>

* **Explore Splunk:** Direct links to the documentation and the wizards used for getting new data into the system.

Question: In the Add Data tab, which option is used to collect data from files and ports?

> **Answer:** Monitor

***

## Task 5: Adding Data

A manual ingestion of `VPN_logs` was performed to evaluate the Splunk pipeline. The process followed five distinct stages: source selection, format definition, input configuration (Index: `VPN_Logs`), review, and final commit.

#### SPL (Search Processing Language) Framework

The following query commands were utilized to perform a forensic analysis of the ingested VPN traffic:

* **`index="VPN_Logs"`**: Restricted the search scope to the relevant data repository for improved performance.
* **`field="value"`**: Targeted specific parsed entities such as usernames or source IPs.
* **`!=`**: An exclusion operator used to filter out noise, such as traffic originating from specific geographic locations.
* **`stats count`**: A statistical command used to aggregate event frequency and determine the volume of specific activities.
* **`table`**: A formatting tool used to present extracted fields in a structured report format.

#### Practical Investigation Findings

Question: Upload the data attached to this task and create an index "VPN\_Logs". How many events are present in the log file? _Search performed: `index="VPN_Logs"`_

> **Answer:** 2862

Question: How many log events are captured by the user Maleena? _Search performed: `index="VPN_Logs" user="Maleena"`_

> **Answer:** 60

Question: What is the username associated with IP 107.14.182.38? _Search performed: `index="VPN_Logs" source_ip="107.14.182.38"`_

> **Answer:** Smith

Question: What is the number of events that originated from all countries except France? _Search performed: `index="VPN_Logs" Source_Country!="France"`_

> **Answer:** 2814

Question: How many VPN events were associated with the IP 107.3.206.58? _Search performed: `index="VPN_Logs" source_ip="107.3.206.58"`_

> **Answer:** 14

***

## Conclusion

The room confirmed that Splunk's architecture effectively transforms raw telemetry into forensic clarity. Mastery of the ingestion pipeline and SPL allows SOC analysts to pinpoint specific behaviors within massive datasets, serving as a vital foundation for automated alerting and threat hunting.
