---
icon: windows
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Uptime Kuma & Windows Fi

**Date:** 10.07.2026

**Core Concept**: Infrastructure Monitoring and Expanded Deception Coverage

Today focused on two fronts: bringing monitoring visibility online with Uptime Kuma, and expanding the Windows honeypot's credential surface with a set of weak decoy accounts to finally start capturing post-exploitation behavior.

***

## Section 1: Overview

With the Linux honeypot having already yielded a strong dataset, focus shifted to closing two gaps: the lack of infrastructure-level monitoring across the lab, and the Windows honeypot's missing weak-credential configuration that had been limiting analysis to failed login attempts only.

***

## Section 2: Uptime Kuma Deployment

Uptime Kuma was deployed on the Analysis VM via a straightforward git clone rather than a custom Docker Compose file, keeping the setup aligned with the project's official defaults and reducing the chance of misconfiguration.

```bash
git clone https://github.com/louislam/uptime-kuma.git
cd uptime-kuma
docker compose up -d
```

Kuma listens on port 3001. Since the analysis subnet's inbound rule already allows all ports from the Home IP, no new NSG rule was needed to reach the dashboard itself.

Monitoring the honeypot VMs required a separate consideration. The target subnet's NSG only permits inbound traffic on the honeypot bait ports (22, 23, 3389) and the Home IP admin rule, neither of which Kuma's checks could use, since Kuma originates from the analysis subnet's private IP range, not the Home IP. Checking honeypot health directly against the bait ports was ruled out, since Cowrie and RDP would respond in ways that don't reflect true host health, and would mix monitoring traffic into the same logs used for attacker analysis.

The cleanest solution was a dedicated inbound rule on the target subnet's NSG allowing ICMP from the analysis subnet range, paired with allowing ICMP through each honeypot's local OS firewall. This lets Kuma run simple ping checks against the honeypots' private IPs without touching any bait-facing service or polluting the honeypot logs with monitoring noise.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-10 151453 (1).png" alt=""><figcaption></figcaption></figure>

Both monitors are now live: the Windows honeypot shows a clean "Up" status with sub-millisecond response times. The Linux honeypot currently shows down, which is expected, as Cowrie was intentionally powered off after collecting a strong initial dataset, in order to conserve budget.

***

## Section 3: Windows Honeypot Decoy Accounts

The Windows honeypot has so far only captured failed RDP login attempts, since no account on the VM had a weak enough password to be caught by brute force. To address this, local password complexity requirements and minimum length policy were disabled via secedit, allowing weak passwords to be set without Windows rejecting them outright.

A set of decoy accounts was then created, added to the Remote Desktop Users group so they're reachable via RDP, and left as standard users rather than local administrators. This was a deliberate choice: keeping decoy accounts at standard privilege forces any successful login to attempt privilege escalation before reaching meaningful access, which produces more useful Sysmon telemetry than an account that grants full control immediately.

The following accounts are now active with intentionally weak credentials:

* admin - password123
* guest (built-in account, re-enabled) - admin123
* test - test123
* backup - 123456
* support - 123456
* sql - 123456
* user - 123456
* demo - 123456

<figure><img src="../.gitbook/assets/Screenshot 2026-07-10 145745.png" alt=""><figcaption></figcaption></figure>

These names were chosen as generic, commonly targeted credentials rather than pulling directly from this VM's own observed username data, to keep the bait broad and avoid over-fitting to a single prior sample.

***

## Conclusion

Day 14 closes two operational gaps that had been sitting open since earlier in the project. The lab now has functioning uptime monitoring across both honeypots and the analysis stack, achieved without introducing any new attack surface or contaminating existing log data. The Windows honeypot is now properly baited with a spread of weak-credential accounts at standard privilege, setting up the next round of analysis to finally capture full attack chains, including privilege escalation attempts, rather than brute force attempts alone.
