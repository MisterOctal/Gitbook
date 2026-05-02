---
icon: pen-ruler
cover: >-
  https://cdn.discordapp.com/attachments/1389878975332880476/1500054276263645204/wp3734311.png?ex=69f709e9&is=69f5b869&hm=936a485c093a915bd797b2c1abcf5fcd0143f3b99b491b22fdeb9b0591735c5e
coverY: 0
coverHeight: 138
---

# Day 0 - Lab Planning

## Section 1: The Pivot to Azure

Initially, this SOC lab was conceptualized for a local hypervisor environment. However, to better align with industry standards and the evolving landscape of cybersecurity, the decision was made to migrate the entire infrastructure to **Microsoft Azure**.

### Why the switch to Cloud-Native?

* **Scalability & Flexibility:** Azure allows for the rapid deployment and teardown of resources. In a SOC environment, being able to scale the "Analysis Zone" based on log volume is a critical real-world skill.
* **Exposure to Enterprise Tools:** Utilizing Azure provides native integration with **Microsoft Sentinel**, a Tier-1 SIEM/SOAR platform used by Global 500 companies.
* **Accessibility:** A cloud-based lab is accessible from anywhere, removing the hardware limitations of a local machine and allowing for 24/7 potential uptime for honeypot telemetry gathering.
* **Cost Management:** By using B-Series burstable VMs and automated shutdown schedules, we can maintain a high-performance lab within a strict student budget.

***

## Section 2: Logical Architecture

The lab follows a "Hybrid-Analysis" model. It is designed to capture high-volume "noise" from the internet via honeypots, process that data in a cost-effective ELK stack, and escalate critical alerts to Microsoft Sentinel for incident response.

<figure><img src="../.gitbook/assets/azure soc lab.drawio (2).png" alt=""><figcaption></figcaption></figure>

### Architectural Breakdown

* **Target Zone (The Honeypots):** Exposed VMs running Windows (Sysmon/Winlogbeat) and Linux (Cowrie/Filebeat). These are placed in a permissive subnet to attract global brute-force and exploit attempts.
* **Analysis Zone (ELK Stack + Docker):** The "Brain" of the operation. A centralized Linux host running Elasticsearch, Logstash, and Kibana.
  * **Docker Integration:** This host also runs **Uptime Kuma** in a Docker container to provide real-time availability monitoring for the entire lab.
* **SIEM Zone (Microsoft Sentinel):** The professional orchestration layer. It handles filtered, high-fidelity alerts and serves as the primary interface for KQL-based threat hunting.
* **Management Zone:** A restricted access layer for management.&#x20;
* Testing and verification attacks are performed from a **Local Attacker Machine** (External to Azure) to simulate a realistic external threat actor.

***

## Section 3: Infrastructure Inventory

The following virtual assets will be provisioned within a single Azure Resource Group. The Attacker role is handled by a local machine to reduce cloud overhead.

<table data-header-hidden><thead><tr><th width="187.5999755859375"></th><th width="150.199951171875"></th><th width="133.7999267578125"></th><th></th></tr></thead><tbody><tr><td>Role</td><td>OS</td><td>Size</td><td>Key Components</td></tr><tr><td><strong>SOC-ELK-HOST</strong></td><td>Ubuntu 22.04</td><td>Standard_B2s</td><td>Docker, Elasticsearch, Logstash, Kibana, Uptime Kuma</td></tr><tr><td><strong>HONEY-WIN</strong></td><td>Windows Server</td><td>Standard_B2s</td><td>Sysmon, Winlogbeat, Exposed RDP/SMB</td></tr><tr><td><strong>HONEY-LINUX</strong></td><td>Ubuntu 22.04</td><td>Standard_B1s</td><td>Cowrie Honeypot, Filebeat, Exposed SSH/Telnet</td></tr><tr><td><strong>LOCAL-ATTACKER</strong></td><td>Kali Linux</td><td>Local</td><td>Nmap, Metasploit, Hydra (Simulates external actor)</td></tr></tbody></table>

***

## Section 4: Implementation Roadmap

### Phase 1: Foundation (Azure Networking)

* Deploy Virtual Network (VNet) with partitioned subnets.
* Provision all VMs with **Standard SSD** storage to balance cost and performance.
* Configure **Network Security Groups (NSGs)**:
  * _Restrictive:_ Management subnet locked to researcher's Public IP.
  * _Permissive:_ Honeypot subnet open to `0.0.0.0/0` on specific attack ports to invite global telemetry.

### Phase 2: Analysis & Monitoring (ELK + Docker)

* Install Docker Engine on the ELK Host.
* Deploy **Uptime Kuma** container to monitor VM heartbeats.
* Install and configure the ELK Stack (Elasticsearch, Logstash, Kibana).
* Verify Kibana dashboard accessibility.

### Phase 3: Telemetry Ingestion (Honeypot Deployment)

* Deploy **Cowrie** on Linux to simulate a vulnerable SSH server.
* Deploy **Sysmon** on Windows for deep forensic logging.
* Configure **Beats agents** to ship raw logs to the ELK Host via private IP (Port 5044).
* Perform **Connectivity Testing:** Use the Local Attacker Machine to perform basic port scans against the Honeypot Public IPs to verify log ingestion.

### Phase 4: SIEM & SOAR (Microsoft Sentinel)

* Enable Microsoft Sentinel on a Log Analytics Workspace.
* Connect Azure Activity Logs to monitor infrastructure-level threats.
* Implement a filtered log pipeline (Manual CSV or Logstash Plugin) to move critical events from ELK to Sentinel.
* Develop **KQL Detection Rules** and **Automation Playbooks** (Logic Apps) for incident response.

***

## Section 5: Risk & Cost Mitigation

### Operational Safeguards

* **On-Demand Operation:** To maximize credit longevity, the lab will operate on an "On-Demand" basis rather than a 24/7 cycle. VMs will only be active during active research and data collection windows.
* **Strict Auto-Shutdown:** All lab assets are configured with a hard **Auto-shutdown at 21:00 (9:00 PM)**. This ensures that even if a session is left active, the VMs will not burn credits overnight.
* **Network Isolation:** The Target Zone (Honeypots) is logically isolated from the Analysis and Management zones. This prevents lateral movement should an attacker successfully breach the honeypot software.
* **Budget Tracking:** Azure consumption alerts will also be configured to prevent unexpected billing.
