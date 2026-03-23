---
icon: info
cover: >-
  https://cdn.discordapp.com/attachments/1031890307979149332/1483977967347433593/image.png?ex=69c1d3ae&is=69c0822e&hm=25522067af4b6d5ef6422a90b24711fb0f5e62d1bd0b48c156779b9ffc173adc
coverY: 0
coverHeight: 138
---

# Day 0 - Lab Summary

**Date:** 14.03.2026

## Section 1: Project Goals

The primary goal of this Home Security Operations Center (SOC) lab is to provide practical, hands-on SOC experience and to actively improve my cybersecurity knowledge. Rather than just a learning environment, this lab is designed to serve as the centerpiece of my professional portfolio, showcasing my ability to build, attack, defend, and monitor network environments.

***

## Section 2: Architecture & Environment

### Current Infrastructure

At present, the lab is entirely virtualized and hosted locally, all VMs are stored in `D:\Homelab`.

* **Host Machine:** MSI GF63 Thin 9SCXR Laptop
  * **Host OS:** Windows 11
  * **RAM:** 16 GB DDR4
  * **CPU:** Intel<sup>(R)</sup> Core<sup>(TM)</sup> i7-9750H CPU @ 2.60GHz
  * **GPU:** The NVIDIA GeForce GTX 1660 Ti with Max-Q Design
  * **Note:** _I will likely migrate to a better laptop later this year to prepare for my departure for studying abroad_
* **Hypervisor:** VMware Workstation Pro
* **Virtual Machines:**
  * **Attacker:** Kali Linux
  * **Target:** Metasploitable 2
* **Networking:** Both virtual machines are strictly isolated to a host-only network configuration. This ensures safety by preventing any malicious traffic or vulnerable exposures from leaking onto my physical local area network or the public internet.

### Why VMware?

VMware was chosen as the hypervisor for this project because it is an industry standard and the most popular virtualization platform in enterprise environments. Additionally, it is the primary software utilized by my college, allowing me to bridge my academic studies with my personal portfolio work seamlessly.

***

## Section 3: Overall Plan & Roadmap

### Development Philosophy

This project has **no strict time limit** (e.g., this is not a "100 Days of SOC" challenge). The lab is designed to be a living, breathing environment that will grow and evolve continuously as my own skills and knowledge expand.

However, development on this lab may be paused/slowed when college coursework gets too heavy.

### Phased Approach

The current roadmap for the lab's evolution is broken down into the following phases:

* **Phase 1: The "Tug of War" (Current)** This initial phase focuses on a continuous cycle of attack and defense between Kali Linux and Metasploitable 2.
  * Kali launches an attack and compromises the target.
  * The vulnerability on Metasploitable 2 is analyzed, mitigated, and patched.
  * Kali attempts the same attack and fails.
  * Kali researches and launches a new attack vector.
  * _The cycle repeats._
* **Phase 2: Escalation & Metasploitable 3** Once the Metasploitable 2 environment has been thoroughly explored and secured, I will install and configure Metasploitable 3. This will introduce more modern vulnerabilities and complex configurations, and the "Tug of War" cycle will begin again with this new target.
* **Phase 3: Centralized Monitoring** After establishing a solid foundation of attack and defense mechanics, the next major milestone will be to deploy a Security Information and Event Management (SIEM) solution. This will shift the focus towards blue-team operations: log aggregation, threat hunting, creating alerts, and monitoring the ongoing "Tug of War" in real-time.
