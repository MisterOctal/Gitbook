---
icon: globe
cover: >-
  https://cdn.discordapp.com/attachments/1389878975332880476/1500054276263645204/wp3734311.png?ex=69f709e9&is=69f5b869&hm=936a485c093a915bd797b2c1abcf5fcd0143f3b99b491b22fdeb9b0591735c5e
coverY: 0
coverHeight: 139
---

# Day 1 - NSG Configurations

**Date:** 02.05.2026

**Core Concept:** Network Micro-segmentation and Build-Phase Hardening.

Establishing the initial Network Security Group rules was necessary to create a secure environment for lab deployment. These configurations ensure that administrative access is restricted while the monitoring infrastructure is being built.

***

## Section 1: Management Zone Configuration

The Management Zone acts as the secure entry point for the lab owner. The primary configuration focus for this zone was restricted administrative access.

A high priority rule was implemented to allow all protocols and ports only from a specific whitelisted home IP address.

This ensures that the management subnet remains invisible to the public internet. It allows the administrator to perform initial VM provisioning and software installations without network friction.

<table data-header-hidden><thead><tr><th width="172.800048828125"></th><th></th><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><td>Rule Name</td><td>Priority</td><td>Port</td><td>Protocol</td><td>Source</td><td>Action</td></tr><tr><td>Allow-MyIP-Admin</td><td>100</td><td>Any</td><td>Any</td><td>[HOME_IP]</td><td>Allow</td></tr><tr><td>DenyAllInBound</td><td>65500</td><td>Any</td><td>Any</td><td>Any</td><td>Deny</td></tr></tbody></table>

***

## Section 2: Analysis Zone Configuration

The Analysis Zone was configured to support the telemetry ingestion and monitoring stack. Inbound rules were established to allow traffic on port 5044 for Beats log shippers.

Additional inbound access was granted for port 3001 for internal service monitoring from within the virtual network.

Outbound security was tightened significantly by allowing egress only to the AzureMonitor service tag on port 443. A specific rule was also added to allow Docker image pulls.

An explicit deny rule at priority 1000 ensures that no unauthorized external communication occurs from the SIEM environment.

<table data-header-hidden><thead><tr><th width="176.79998779296875"></th><th></th><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><td>Rule Name</td><td>Priority</td><td>Port</td><td>Protocol</td><td>Direction</td><td>Action</td></tr><tr><td>Allow-Beats-Logs</td><td>200</td><td>5044</td><td>TCP</td><td>Inbound</td><td>Allow</td></tr><tr><td>Allow-Kuma-Internal</td><td>210</td><td>3001</td><td>TCP</td><td>Inbound</td><td>Allow</td></tr><tr><td>Allow-To-Sentinel</td><td>100</td><td>443</td><td>TCP</td><td>Outbound</td><td>Allow</td></tr><tr><td>Allow-Web-Access</td><td>110</td><td>443</td><td>TCP</td><td>Outbound</td><td>Allow</td></tr><tr><td>Deny-All-Internet</td><td>1000</td><td>Any</td><td>Any</td><td>Outbound</td><td>Deny</td></tr></tbody></table>

***

## Section 3: Honeypot Zone Configuration

The Honeypot Zone was configured with a strict build phase posture to prevent early compromise. Although this zone will eventually be exposed to the internet, the current inbound rules for SSH on port 22 and RDP on port 3389 are set to an explicit Deny state for the general public.

Administrative access is permitted only via the whitelisted home IP. This allows for the safe installation of honey services and logging agents before the environment is intentionally made vulnerable.

This isolation is critical during the build phase to ensure that no live attacks interfere with the baseline setup of the virtual machines.

<table data-header-hidden><thead><tr><th width="174.4000244140625"></th><th></th><th></th><th></th><th></th><th></th></tr></thead><tbody><tr><td>Rule Name</td><td>Priority</td><td>Port</td><td>Protocol</td><td>Source</td><td>Action</td></tr><tr><td>Allow-MyIP-Admin</td><td>100</td><td>Any</td><td>Any</td><td>[HOME_IP]</td><td>Allow</td></tr><tr><td>Allow-SSH-Bait</td><td>200</td><td>22</td><td>TCP</td><td>Internet</td><td>Deny</td></tr><tr><td>Allow-RDP-Bait</td><td>210</td><td>3389</td><td>TCP</td><td>Internet</td><td>Deny</td></tr></tbody></table>

***

## Conclusion

The first day of the lab build successfully established a secure foundation. By hardening the network perimeter before deploying any services, the risk of a premature compromise is minimized.

I have also uploaded the scrubbed versions of these configurations to my GitHub repository to serve as a proof of methodology and to document the build process.

The focus now shifts to deploying the monitoring agents and refining the outbound isolation for the honeypot VMs.
