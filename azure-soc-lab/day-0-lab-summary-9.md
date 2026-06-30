---
icon: globe
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 9 - Public Exposure

**Date:** 30.06.2026

**Core Concept**: Public Honeypot Exposure and Passive Intelligence Collection

Day 9 was a single-task day: update the Network Security Group rules to expose both honeypots to the public internet. With this change, automated scanners and bots would begin attacking the honeypots immediately, generating real-world telemetry for analysis.

***

## Section 1: NSG Rule Updates

The `nsg-target-honeypots` Network Security Group was updated with inbound rules to allow both honeypots to receive traffic from the public internet while maintaining restricted administrative access on non-standard ports.

**Final NSG Inbound Configuration:**

<table><thead><tr><th width="98.4000244140625">Priority</th><th>Rule Name</th><th>Port(s)</th><th width="101.800048828125">Source</th><th width="116.2000732421875">Action</th><th>Purpose</th></tr></thead><tbody><tr><td>100</td><td>Admin-Allow-Access</td><td>All</td><td>Home IP</td><td>Allow</td><td>All admin access from Home IP</td></tr><tr><td>200</td><td>Honeypots</td><td>22, 23, 3389</td><td>0.0.0.0/0</td><td>Allow</td><td>Honeypot bait ports to internet</td></tr></tbody></table>

Administrative access remained restricted to the Home IP. The honeypot bait ports (22 for SSH, 23 for Telnet, 3389 for RDP) were fully exposed to the public internet.

***

## Section 2: Passive Intelligence Collection Mode

With both honeypots now exposed and both ELK and Filebeat/Winlogbeat running continuously, the lab transitioned into passive intelligence collection mode. The infrastructure remains deployed and logging, but no active analysis or configuration work is required.

The system operates continuously:

* Cowrie logs all SSH and Telnet connection attempts to `cowrie.json`
* Sysmon captures Windows process and network activity
* Filebeat and Winlogbeat ship logs to Logstash on a rolling basis
* Elasticsearch indexes all events by date
* Kibana dashboards remain accessible for ad-hoc querying

Real attack traffic has begun flowing into the honeypots immediately. The dual-source telemetry provides a realistic view of automated attack patterns across both Linux and Windows platforms.

***

## Conclusion

Day 9 is complete. Both honeypots are now fully exposed to the public internet and generating real-world attack telemetry. The SOC pipeline is operational and running in passive collection mode. From this point forward, the lab will accumulate weeks of attack data suitable for analysis, pattern identification, and alert tuning.
