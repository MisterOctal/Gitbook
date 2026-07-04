---
icon: building-magnifying-glass
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 10 - Checkup

**Date:** 03.07.2026

**Core Concept**: Passive Collection Verification and Data Health Check

Day 10 was a brief operational checkpoint. Both honeypots had been accumulating attack data for three days while ELK was offline. The goal was to verify that Filebeat and Winlogbeat reconnected successfully and that real-world bot traffic had been captured during the offline period.

***

## Section 1: ELK Startup and Reconnection

The ELK VM was brought online and the Docker stack was started:

```bash
cd ~/docker-elk
sudo docker compose up -d
```

Within 2-3 minutes, all three services (Elasticsearch, Logstash, Kibana) were operational. Filebeat and Winlogbeat on both honeypots automatically detected the reconnected Logstash endpoint and began shipping queued events.

***

## Section 2: Data Verification

Both honeypots had accumulated significant attack telemetry during the three-day offline period:

**Cowrie (Linux SSH/Telnet):** Multiple days of brute force attempts, login probes, and filesystem exploration were captured in the queued JSON logs. Filebeat successfully shipped all accumulated events into the `cowrie-*` indices.

**Sysmon/Winlogbeat (Windows):** RDP brute force attempts, failed authentication events (Event ID 4625), and process activity were captured in the Windows Event Log and shipped into the `winlogbeat-*` indices.

Both data sources confirmed that real automated bot traffic had been actively scanning and attacking both honeypots throughout the entire offline period.

***

## Section 3: Data Health and Quality

<figure><img src="../.gitbook/assets/Screenshot 2026-07-03 115844 (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-07-03 120040.png" alt=""><figcaption></figcaption></figure>

Elasticsearch indices contained well-formed events with correctly parsed fields:

* Source IPs were visible and extractable
* Timestamps were accurate and aligned with the attack timeline
* Event structures were consistent and queryable

The volume of data was substantial, enough to provide meaningful attack pattern analysis when reviewed at a later date.

***

## Section 4: Transition to Passive Collection

With both honeypots confirmed as healthy and generating real-world telemetry, the lab was transitioned to long-term passive collection mode. The honeypots were deallocated to halt Azure compute charges, but left configured to continue logging locally. Both Cowrie and Sysmon remained active and would continue accumulating attack data indefinitely.

The ELK stack was shut down to preserve credits. When analysis work resumes, ELK will be brought online and the accumulated logs will ship automatically.

***

## Section 5: Identified Improvements for Future Iterations

During the data review, one weakness was identified: while Cowrie captured extensive login attempt data, virtually no attackers actually succeeded in gaining shell access. This meant the captured telemetry showed only the initial attack phase, not post-compromise behavior.

The next iteration will update the Cowrie userdb with weaker, more generic credentials that automated bots are likely to guess. Rather than blocking all logins, the honeypot will allow successful compromises for a subset of common usernames (admin, test, user) with simple passwords (admin123, password, test123). This will capture post-exploitation behavior: commands run by attackers, files accessed, lateral movement attempts, and tooling deployed. The richer dataset will provide better insight into attacker tactics beyond initial access.

***

## Conclusion

Day 10 is complete. Both honeypots have been verified as healthy and capturing real bot traffic. Three days of attack telemetry has been accumulated and is ready for later analysis. The lab is now in passive collection mode, generating security intelligence continuously while consuming minimal cloud resources.
