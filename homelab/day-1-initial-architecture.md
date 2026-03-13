---
icon: hammer
---

# Day 1 - Initial Architecture

Written on: 12.03.2026

Welcome to Day 1 of my home SOC lab project!

Before deploying complex SIEMs, configuring log forwarders, or writing custom detection alerts, I need a reliable and secure foundation. Today's focus is entirely on planning the architecture and outlining the networking strategy before any actual configurations or installations take place.

The initial scope for Week 1 is kept intentionally small: one attacker machine and one highly vulnerable target machine.

***

## Section 1: Lab Architecture Overview

For this initial phase, this lab consists of two virtual machines communicating over an isolated, air-gapped, virtual network.

* **Hypervisor:** VMware Workstation
* **Host OS:** Windows 11
* **Host Machine Specs:**
  * **Laptop Model:** GF63 Thin 9SCXR
  * **Processor:** Intel(R) Core(TM) i7-9750H CPU @ 2.60GHz 2.60 GHz
  *    **Installed RAM:** 16.0 GB (15.8 GB usable)
  * **Graphics Card:** NVIDIA Geforce GTX 1650 with Max-Q Design
* **Network Type:** Host-Only
* **Attacker:** Kali Linux
* **Target:** Metasploitable 2

### Why VMware?

I chose VMware as my hypervisor for this project for two main reasons. First, it is an industry-standard virtualization platform widely used in enterprise environments, making it a valuable tool to master. Second, it is the standard hypervisor used in my college courses, which allows me to maintain a consistent workflow between my academic studies and my personal lab.

Below is a diagram of **Percentage of Hypervisors By Manufacturer** ([source](https://www.controlup.com/resources/blog/hypervisor-market-share-controlup-perspective/))

<figure><img src="../.gitbook/assets/image (3).png" alt="" width="375"><figcaption></figcaption></figure>

### The Machines

**1. Kali Linux (The Attacker / Analyst Box)** Kali is an industry-standard, Debian-based Linux distribution packed with penetration testing and security auditing tools. For the first phase of this project, Kali will act as my primary workstation to conduct reconnaissance, launch attacks, and eventually monitor traffic.

**2. Metasploitable 2 (The Target)** Metasploitable 2 is an intentionally vulnerable Ubuntu Linux virtual machine. It is designed for testing security tools and practicing penetration testing techniques. It comes pre-loaded with outdated services, misconfigurations, and open ports, making it the perfect noisy target for my SOC lab.

***

## Section 2: Planned Networking Strategy (Host-Only)

**Security Note:** Metasploitable 2 is fundamentally insecure by design. If exposed to the internet or my local home network, it could easily be compromised by automated scanners or malware outside of my control.

To prevent this, I plan to configure both VMs in VMware to use a **Host-Only Network** (VMnet).

* This ensures the VMs can talk to each other and the host machine.
* They _cannot_ access the public internet.
* Devices on my physical home network _cannot_ access the VMs.

This isolated sandbox will allow me to safely launch exploits and analyze traffic without risking my home network.

***

## Section 3: Kali Linux Dual-Purpose Organization

A SOC analyst spends hours staring at a terminal, so maintaining an organized environment is crucial. Because I am currently completing my Higher National Diploma (HND) at Auston, my Kali Linux instance will serve a dual purpose: my 100-Day SOC Lab and my academic coursework.

To keep my environments strictly organized, I plan to create a dedicated directory structure right from the start. All Auston coursework will be maintained in its own isolated directory, completely separate from my personal security projects.

My planned folder hierarchy will look something like this:

```
~ (Home Directory)
├── Auston_Coursework/
│   ├── assignments/
│   ├── labs/
│   └── research_project/
└── Personal_Projects/
    └── SOCLab/
        ├── recon/
        ├── exploits/
        ├── logs/
        └── notes/
```

This strict separation ensures that my academic files remain untouched by any scripts or tools I run for the SOC lab.

***

## Section 4: Next Steps

Today was all about laying the conceptual groundwork. Tomorrow (Day 2), the actual hands-on work begins. My goals for the next session are:

1. Install Kali Linux and Metasploitable 2 into VMware.
2. Configure the Host-Only network adapter and verify communication via ping tests.
3. Apply system updates to Kali and establish the directory structure outlined above.

The foundation is planned out. Tomorrow, we build it.
