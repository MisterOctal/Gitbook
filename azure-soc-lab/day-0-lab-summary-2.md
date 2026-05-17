---
icon: screwdriver-wrench
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 2 - VNet and Subnet Setup

**Date:** 17.05.2026

**Core Concept**: Network Isolation, Segmentation, and Attack Surface Reduction

Setting up the Virtual Network (VNet) and Network Security Groups (NSGs) today is the most critical infrastructure task of the project. Before we deploy any exposed honeypot virtual machines, we must build the network boundaries that contain them. For our initial build phase, we are implementing a strict staging configuration. This keeps both the Analysis and Target zones completely closed to the public, allowing outbound internet access for updates and software installation while restricting administrative access exclusively to our Home IP.

***

## Section 1: Virtual Network (VNet) Design and Subnetting

We designed and provisioned a single Virtual Network (vnet-soc-lab) with a private IP address space of 10.0.0.0/16. To enforce strict segregation, we carved this space into two dedicated subnets:

<figure><img src="../.gitbook/assets/image (414).png" alt=""><figcaption></figcaption></figure>

* **Analysis Subnet (snet-analysis):**
  * **IP Range:** 10.0.1.0/24
  * **Purpose:** Houses our ELK Server (Elasticsearch, Logstash, Kibana) and the Uptime Kuma monitoring service. This is our secure backend zone.
* **Target Subnet (snet-target):**
  * **IP Range:** 10.0.2.0/24
  * **Purpose:** Houses our Linux and Windows honeypot VMs. During staging, this zone remains locked down. Once setup is complete, it will be the untrusted zone exposed to the public internet.

***

## Section 2: Staging Network Security Groups (NSGs)

Rather than opening the honeypots to the public internet immediately, we configured both NSGs in a locked down staging state to protect the VMs during their vulnerable configuration phase.

<figure><img src="../.gitbook/assets/image (416).png" alt=""><figcaption></figcaption></figure>

* **NSG Analysis (nsg-analysis-prod):**
  * Associated with `snet-analysis`.
  * **Rule 1 (Admin Access):** Allow inbound SSH (port 22) and HTTP/HTTPS (port 5601 for Kibana) ONLY from our specific Home IP address. All other internet traffic to these administrative ports is dropped.
  * **Rule 2 (Log Ingestion):** Allow inbound TCP port 5044 (Logstash Beats port) specifically from the `snet-target` IP range. This allows the honeypot logging agents to ship their logs securely.

<figure><img src="../.gitbook/assets/image (417).png" alt=""><figcaption></figcaption></figure>

* **NSG Target (nsg-target-honeypots) \[Staging Mode]:**
  * Associated with `snet-target`.
  * **Rule 1 (Admin Access Only):** Temporarily allow inbound SSH (port 22) and inbound RDP (port 3389) ONLY from our specific Home IP address.
  * **Rule 2 (Public Block):** All inbound traffic from the general public internet (0.0.0.0/0) is explicitly blocked. The bait ports (22, 23, 445, 3389) are not yet exposed to the world.

***

## Section 3: Outbound Connectivity and Lateral Movement

To ensure we can configure our staging environment and eventually ship logs safely, we established the following routing and outbound rules:

* **Outbound Internet Access:** Both subnets are configured to allow outbound connections to the internet. This is essential for both the ELK server and the honeypots to download system updates, pull Docker microservices (such as Uptime Kuma and Cowrie), and install agent packages (Filebeat, Winlogbeat, and Sysmon).
* **Preventing Lateral Movement:** Even in staging, we enforce isolation. We created an outbound rule in `nsg-target-honeypots` that blocks all traffic destined for `snet-analysis` (10.0.1.0/24).
* **Logging Exception:** We placed a higher priority rule above the lateral movement block to explicitly allow TCP traffic over port 5044 to the private IP address of the ELK VM. This ensures that our agents can register with Logstash even while general network access between the subnets is denied.

***

## Section 4: Strategic Exclusions (Why No Firewall, Bastion, DDoS, or NAT Gateway?)

In an enterprise environment, security teams deploy a variety of native cloud security services. However, for a cost restricted security research lab, these services are not only financially impossible but also functionally counterproductive.

* **Azure Firewall (Excluded due to Cost and Function):**
  * **The Cost Barrier:** Azure Firewall Basic costs approximately $290 per month, while the Standard tier easily exceeds $900 per month. Running this service for even a week would completely wipe out our $100 annual student credit.
  * **The Functional Conflict:** Enterprise firewalls are designed to inspect and block malicious or anomalous traffic. Because the entire purpose of our target subnet is to invite, capture, and study raw malicious attacks, an active firewall would filter out the very data we are trying to collect.
* **Azure Bastion (Excluded due to Cost):**
  * **The Cost Barrier:** Azure Bastion Developer or Basic tiers cost between $14 and $20 per month. While this is highly secure, it would consume 15% to 20% of our total yearly budget every single month just for administrative access.
  * **The Alternative:** We achieve equivalent, zero-cost protection by configuring our NSGs to block administrative ports (like SSH and RDP) to everyone except our specific Home IP address.
* **Azure DDoS Protection (Excluded due to Scale and Cost):**
  * **The Cost Barrier:** Azure DDoS IP Protection costs around $199 per month per resource, and Network Protection costs roughly $2,944 per month. This is designed for massive enterprises maintaining 100% web uptime.
  * **The Security Reality:** Our honeypots are temporary, on-demand, and hold zero critical data. If an attacker decides to launch a DDoS attack against our honeypot public IPs, we can simply shut down the VMs, delete the affected public IP resources, and generate new ones for free.
* **Azure NAT Gateway (Excluded due to Inbound Traffic Flow and Cost):**
  * **The Cost Barrier:** Azure NAT Gateway charges $0.045 per hour for the gateway resource (roughly $32 per month) plus $0.045 per GB for data processing. Keeping this running 24/7 would consume almost a third of our annual budget.
  * **The Functional Block:** A NAT Gateway only facilitates secure outbound connections (Source NAT). It explicitly prevents any inbound connections initiated from the public internet. Since our target subnet hosts honeypots that must receive direct inbound attacks (SSH, RDP, SMB) from outside scanners, routing our target traffic through a NAT Gateway would render our honeypots completely unreachable.
  * **The Public IP Alternative:** Instead of placing the VMs on a private subnet behind a NAT Gateway, we assign instance-level public IPs directly to our honeypots. This enables direct inbound attacks while our strict NSG rules protect the backend analysis systems.

***

## Conclusion

With the VNet deployed, subnets carved, and staging NSG rules applied, our secure staging ground is ready. We have created a secure configuration where we can build, update, and configure our systems privately. Outbound internet access is fully functional, but incoming attacks are completely blocked. Tomorrow, we move into Day 3: Deploying the Linux Honeypot VM, installing Docker, and configuring the Cowrie SSH honeypot.
