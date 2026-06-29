---
icon: windows
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 8 - Windows Honeypot

**Date:** 29.06.2026

**Core Concept**: Dual-Honeypot Architecture and ELK Pipeline Integration

Day 8 expanded the lab from a single Linux honeypot to dual-honeypot architecture. A Windows Server 2022 VM was deployed alongside the existing Cowrie honeypot, both configured to ship telemetry into the unified ELK pipeline. By session end, both honeypots were logging and indexing events successfully.

***

## Section 1: Windows Honeypot Provisioning

A Windows Server 2022 VM was deployed in the same target subnet as the Linux honeypot, providing a second attack surface with native Windows event logging and process telemetry via Sysmon.

**VM Specs:**

* Name: `vm-windows-honeypot`
* Image: Windows Server 2022 Datacenter
* Size: Standard\_B2s (2 vCPU, 4GB RAM)
* Subnet: `snet-target` (10.0.2.0/24)
* NSG: `nsg-target-honeypots`
* Storage: 127GB Standard SSD

After initial provisioning, Windows Updates completed on first boot. Defender and firewall were disabled to reduce log noise.

<figure><img src="../.gitbook/assets/Screenshot 2026-06-29 170508.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-06-29 170544.png" alt=""><figcaption></figcaption></figure>

***

## Section 2: Sysmon Installation and Configuration

Sysmon was installed to capture process creation, network connections, and file write activity like key indicators of compromise on Windows systems.

Sysmon was downloaded from Sysinternals and installed with minimal configuration focused on high-value events:

```powershell
.\Sysmon64.exe -accepteula -i sysmon-config.xml
```

The configuration captured:

* All non-system process creation
* All network connections initiated by processes
* File creation in user directories

This approach generates actionable telemetry without overwhelming the ELK pipeline with routine Windows system noise.

Sysmon events were confirmed visible in the Windows Event Log under `Microsoft-Windows-Sysmon/Operational`.

***

## Section 3: Winlogbeat Installation

Winlogbeat was installed on the Windows VM to ship Security, System, and Sysmon event logs to Logstash. Rather than creating a custom configuration, the default Winlogbeat YAML was used with minimal adjustments to point at the correct Logstash endpoint on the ELK VM.

Winlogbeat was installed as a Windows Service and started automatically using powershell.

***

## Section 4: Logstash Configuration Adjustment

Rather than forcing Winlogbeat to conform to a custom output schema, the approach was inverted: Logstash was configured to handle both Cowrie events (with `source: cowrie_honeypot`) and Windows events (with `source: windows_honeypot`) arriving from their respective beats.

The Logstash filter was extended with a second conditional to route Windows events to `winlogbeat-*` indices while Cowrie events continued to flow to `cowrie-*` indices:

```json
if [source] == "windows_honeypot" {
  mutate {
    add_tag => ["windows", "honeypot"]
    add_field => { "[@metadata][index_name]" => "winlogbeat-%{+YYYY.MM.dd}" }
  }
}
```

This unified approach, using Beats' default behavior and adapting Logstash's pipeline logic proved simpler and more maintainable than custom Beats configurations.

***

## Section 5: Verification and Readiness

Both honeypots were confirmed operational locally. Sysmon was logging process and network activity on the Windows VM. Winlogbeat successfully shipped logs to Logstash, which indexed them in Elasticsearch under the `winlogbeat-*` pattern. Kibana data views were created for both `cowrie-*` and `winlogbeat-*` indices, confirming the dual-source pipeline was functional and ready.

<figure><img src="../.gitbook/assets/Screenshot 2026-06-29 204238.png" alt=""><figcaption></figcaption></figure>

The honeypots are now staged and ready for public exposure. Tomorrow, NSG rules will be updated to open ports 22, 23 (Linux), and 3389 (Windows) to the public internet, beginning real-world bot traffic capture.

***

## Conclusion

Day 8 is complete. The Windows honeypot is provisioned and integrated into the ELK pipeline alongside the existing Linux honeypot. Both systems are logging locally and shipping events to Elasticsearch. The dual-honeypot architecture is ready for public exposure. Tomorrow, both honeypots will be exposed to the internet to begin capturing real automated attack traffic.
