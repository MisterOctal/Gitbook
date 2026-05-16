---
icon: pen-ruler
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 0 - Lab Planning

**Core Concept**: Cost-Effective Cloud Security Engineering and Architecture

Setting up the lab today was a critical "Day 0" task to ensure the longevity of the project. Operating within a $100 student credit limit requires immediate governance through budgets and an "on-demand" operational strategy. By defining the architecture and log-tiering flow before deployment, we avoid accidental overspending on high-volume data ingestion in Microsoft Sentinel.

***

## Section 1: Lab Mission and Objectives

The primary goal is to build a functional, manual Security Operations Center (SOC) environment. Unlike pre-packaged solutions, this manual build provides hands-on experience with system administration and network security.

* **Cost Preservation:** We aim to keep the lab alive for a full year by using a strict $10/month budget and shutting down resources when they are not in active use.
* **Log Tiering Mastery:** We are implementing a "Filter First" approach. Raw logs go to ELK (low cost), and only high-fidelity alerts move to Sentinel (higher cost).
* **Adversary Analysis:** By exposing vulnerable honeypots, we capture real-world attack data to study modern threat actor tactics.

***

## Section 2: Zone-Based Architecture

The network is divided into four distinct zones to maintain security and control the flow of data.

<figure><img src="../.gitbook/assets/azure soc lab.drawio (2) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Management Zone:** The entry point for administrative tasks. It is restricted to a single Home IP to prevent unauthorized access to the control plane.
* **Target Zone (The Honeypots):** This contains the Windows and Linux VMs. They are open to the internet to attract automated scans and brute-force attempts.
* **Analysis Zone (ELK & Docker):** The central hub where raw logs are received, parsed, and stored. It also monitors the health of the other VMs via Uptime Kuma.
* **SIEM Zone (Microsoft Sentinel):** The final escalation point where KQL queries and Logic Apps turn filtered logs into actionable security incidents.

***

## Section 3: Data Ingestion and Escalation Strategy

To protect the Azure budget, we do not send all telemetry to the cloud-native SIEM.

1. **Raw Collection:** Filebeat and Winlogbeat ship every event from the honeypots to Logstash.
2. **Filtering:** Logstash applies logic to separate "noise" (automated scans) from "signals" (successful logins or file drops).
3. **Escalation:** Only the "signals" are forwarded to the Log Analytics Workspace for analysis in Microsoft Sentinel.

***

## Conclusion

The groundwork is now laid for a secure and sustainable lab. By focusing on budget governance and architectural separation today, we have created a safe environment to begin deploying the virtual machines and honeypot services without the risk of exhausting our credits prematurely.
