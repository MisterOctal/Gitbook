---
icon: mountain
cover: https://assets.tryhackme.com/additional/pyramidofpain/updated/banner.png
coverY: 0
coverHeight: 139
---

# Summit

**Date:** 13.04.2026

**Room Category:** Challenge

**Core Concept:** Progressing through the Pyramid of Pain by detecting and blocking increasingly sophisticated malware samples during an iterative SOC simulation.

This room simulates a realistic adversary engagement where a red team operator systematically upgrades their malware to evade detection. It is a highly practical exercise that demonstrates the necessity of moving beyond simple indicators like file hashes and IP addresses, ultimately focusing on behavioral anomalies and adversary procedures to achieve resilient cyber defense.

***

## sample1.exe

The first sample was easily defeated by basic signature-based detection. We scanned the malware payload, identified its MD5 hash, and added it to the hash manager to block its execution.

An image of the first email:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 121816.png" alt=""><figcaption></figcaption></figure>

An image of the malware sandbox for sample1.exe:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 121901.png" alt=""><figcaption></figcaption></figure>

An image of the hash manager after blocking sample1.exe:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 121920.png" alt=""><figcaption></figcaption></figure>

Question: What is the first flag you receive after successfully detecting sample1.exe?

> **Answer:** THM{f3cbf08151a11a6a331db9c6cf5f4fe4}

***

## sample2.exe

The adversary recompiled the malware to alter its hash, rendering our initial defense useless. To counter this, we analyzed its network activity and banned the specific IP address acting as its Command & Control (C\&C) server by configuring the firewall to block all egress traffic to that destination.

An image of the second email:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 121930.png" alt=""><figcaption></figcaption></figure>

An image of sample2.exe's network activity according to the malware sandbox:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122055.png" alt=""><figcaption></figcaption></figure>

An image of the firewall rules after blocking the C\&C server:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122144.png" alt=""><figcaption></figcaption></figure>

Question: What is the second flag you receive after successfully detecting sample2.exe?

> **Answer:** THM{2ff48a3421a938b388418be273f4806d}

***

## sample3.exe

The attacker circumvented the IP ban by rotating through new public IP addresses via a cloud service provider. We escalated our defensive posture by identifying the core C\&C domain routing the traffic and banned the entire domain using the DNS manager.

An image of the third email:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122235.png" alt=""><figcaption></figcaption></figure>

An image of sample3.exe's DNS requests and connections according to the malware sandbox:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122309.png" alt=""><figcaption></figcaption></figure>

An image of the DNS rule manager after blocking the C\&C domain:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122504.png" alt=""><figcaption></figcaption></figure>

Question: What is the third flag you receive after successfully detecting sample3.exe?

> **Answer:** THM{4eca9e2f61a19ecd5df34c788e7dce16}

***

## sample4.exe

With the adversary rapidly registering new domains and rotating DNS records, network indicators were no longer reliable. We shifted our focus to host-level artifacts left behind during execution. We neutralized the threat by blocking the malware's registry activity, specifically denying access to \[X] using Sigma's Sysmon registry modifications blocker.

An image of the forth email:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122519.png" alt=""><figcaption></figcaption></figure>

An image of sample4.exe's registry activity according to the malware sandbox:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122555.png" alt=""><figcaption></figcaption></figure>

An image of the Sigma sysmon registry modifications rule used to stop sample4.exe:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122808.png" alt="" width="543"><figcaption></figcaption></figure>

The Sigma rule generated to stop sample4.exe:

```yaml
title: Modification of Windows Defender Real-Time Protection
id: windows_registry_defender_disable_realtime
description: |
  Detects modifications or creations of the Windows Defender Real-Time Protection DisableRealtimeMonitoring registry value.
references:
  - https://attack.mitre.org/tactics/TA0005/
tags:
  - attack.ta0005
  - sysmon
detection:
  selection:
    EventID: 4663
    ObjectType: Key
    ObjectName: 'HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection'
    NewValue: 'DisableRealtimeMonitoring=1'
  condition: selection
falsepositives:
  - Legitimate changes to Windows Defender settings.
level: high
```

Question: What is the fourth flag you receive after successfully detecting sample4.exe?

> **Answer:** THM{c956f455fc076aea829799c0876ee399}

***

## sample5.exe

The adversary moved their operational logic to a back-end server, altering their local artifacts and protocols to evade detection. By reviewing the outgoing network connection logs from the previous 12 hours, we identified a highly specific behavioral anomaly: the malware was beaconing exactly 97 bytes of data every 30 minutes (1800 seconds). Banning this exact network package pattern stopped the communication.

An image of the fifth email:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 122904.png" alt=""><figcaption></figcaption></figure>

The web server logs generated by sample5.exe (the C\&C beacons are highlighted):&#x20;

<pre class="language-bash"><code class="lang-bash"><strong>2023-08-15 09:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong>2023-08-15 09:23:45 | Source: 10.10.15.12 | Destination: 43.10.65.115 | Port: 443 | Size: 21541 bytes
<strong>2023-08-15 09:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 10:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong>2023-08-15 10:14:21 | Source: 10.10.15.12 | Destination: 87.32.56.124 | Port: 80  | Size: 1204 bytes
<strong>2023-08-15 10:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 11:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 11:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong>2023-08-15 11:45:09 | Source: 10.10.15.12 | Destination: 145.78.90.33 | Port: 443 | Size: 805 bytes
<strong>2023-08-15 12:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 12:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 13:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 13:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong>2023-08-15 13:32:17 | Source: 10.10.15.12 | Destination: 72.15.61.98  | Port: 443 | Size: 26084 bytes
<strong>2023-08-15 14:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 14:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong>2023-08-15 14:55:33 | Source: 10.10.15.12 | Destination: 208.45.72.16 | Port: 443 | Size: 45091 bytes
<strong>2023-08-15 15:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 15:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong>2023-08-15 15:40:10 | Source: 10.10.15.12 | Destination: 101.55.20.79 | Port: 443 | Size: 95021 bytes
<strong>2023-08-15 16:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong>2023-08-15 16:18:55 | Source: 10.10.15.12 | Destination: 194.92.18.10 | Port: 80  | Size: 8004 bytes
<strong>2023-08-15 16:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 17:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong>2023-08-15 17:09:30 | Source: 10.10.15.12 | Destination: 77.23.66.214 | Port: 443 | Size: 9584 bytes
2023-08-15 17:27:42 | Source: 10.10.15.12 | Destination: 156.29.88.77 | Port: 443 | Size: 10293 bytes
<strong>2023-08-15 17:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 18:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 18:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 19:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 19:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 20:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 20:30:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong><strong>2023-08-15 21:00:00 | Source: 10.10.15.12 | Destination: 51.102.10.19 | Port: 443 | Size: 97 bytes
</strong></code></pre>

An image of the Sigma sysmon network connections rule used to stop sample5.exe:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 123201.png" alt="" width="543"><figcaption></figcaption></figure>

The Sigma rule generated to stop sample5.exe:

```yaml
title: Alert on Suspicious Beacon Network Connections
id: network_connections_criteria_sysmon
description: |
  Detects network connections with specific criteria in Sysmon logs: remote IP, remote port, size, and frequency.
references:
  - https://attack.mitre.org/tactics/TA0011/
tags:
  - attack.ta0011
  - sysmon
detection:
  selection:
    EventID: 3
    RemoteIP: '*'
    RemotePort: '*'
    Size: 97
    Frequency: 1800 seconds
  condition: selection
falsepositives:
  - Legitimate network traffic may match this criteria.
level: high
```

Question: What is the fifth flag you receive after successfully detecting sample5.exe?

> **Answer:** THM{46b21c4410e47dc5729ceadef0fc722e}

***

## sample6.exe

For the final payload, the attacker masked all previous indicators, forcing us to target their subconscious operational procedures. By analyzing the recorded command logs from the adversary's previous post-exploitation activity, we identified a consistent procedural habit. We successfully halted their operations by creating a rule to stop the file creation of `exfiltr8.log` within the `%temp%` directory.

An image of the final email:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 123229.png" alt=""><figcaption></figcaption></figure>

An image of the Sigma sysmon file creation and modification rule to stop sample6.exe:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-13 123335.png" alt="" width="542"><figcaption></figcaption></figure>

The Sigma rule generated to stop sample6.exe:

```yaml
title: Alert on Potential Exfiltration through Process Creation
id: sysmon_process_creation_command_line
description: |
  Detects process creation events with specific criteria: process name, and command line containing ">> %temp%\exfiltr8.log".
references:
  - https://attack.mitre.org/tactics/TA0007/
  - https://attack.mitre.org/tactics/TA0009/
  - https://attack.mitre.org/tactics/TA0010/
tags:
  - attack.ta0007
  - attack.ta0009
  - attack.ta0010
  - sysmon
detection:
  selection:
    EventID: 1
    Image:
      - '*\cmd.exe'
      - '*\powershell.exe'
    CommandLine: '*>> %temp%\exfiltr8.log*'
  condition: selection
falsepositives:
  - Legitimate use of cmd.exe or powershell.exe to append data to the %temp% folder.
level: high
```

Question: What is the final flag you receive from Sphinx?

> **Answer:** THM{c8951b2ad24bbcbac60c16cf2c83d92c}

***

## Conclusion

This challenge serves as a practical application of David Bianco's **Pyramid of Pain**. As we progressed from `sample1` to `sample6`, we climbed from the wide, "easy" base of the pyramid (Hash Values) toward the narrow, "tough" apex (Tactics, Techniques, and Procedures).

By reaching the top of the pyramid, we successfully inflicted maximum "pain" on the adversary. While changing a file hash or an IP address is trivial for an attacker, changing their ingrained **TTPs** (such as their specific methods for exfiltrating data) requires significant time, retraining, and resources. Mastering this progression is essential for any SOC analyst aiming to build proactive defenses that survive an adversary's attempts to evolve.
