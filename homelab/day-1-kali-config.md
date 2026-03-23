---
icon: debian
cover: >-
  https://cdn.discordapp.com/attachments/1031890307979149332/1483977967347433593/image.png?ex=69c1d3ae&is=69c0822e&hm=25522067af4b6d5ef6422a90b24711fb0f5e62d1bd0b48c156779b9ffc173adc
coverY: 0
coverHeight: 138
---

# Day 1 - Kali Config

**Date:** 16.03.2026

**Focus:** Base Image Hardening, Localization, and Initial Environment Setup

Day 1 is dedicated to establishing a secure and functional baseline for the SOC Homelab. Moving away from the default Kali Linux configuration is essential to mimic a more realistic environment and to ensure that the investigator's "identity" is clearly defined and protected.

***

## Section 1: Virtual Machine Hardware & Tools Upgrade

Before proceeding with OS-level configurations, a critical preliminary step was performed within the hypervisor. To prevent the notorious "missing cursor" or "stuck mouse" issues often encountered in VMware with Kali Linux, the virtual machine hardware version was upgraded to the latest compatible version.

#### Networking: Dual-Homed Segmentation

To resolve repository connection errors while maintaining a secure, isolated testing environment, a dual-adapter strategy was implemented for the Kali workstation.

* **Adapter 1 (Host-Only):** Configured to connect Kali to the private lab subnet (`VMnet1`). This ensures Kali can communicate with the Metasploitable target without exposing the vulnerable machine to the host network or the internet.
* **Adapter 2 (NAT):** Configured to provide Kali with internet access for system updates (`apt update`) and tool installations.

***

## Section 2: System Updates and Patching

The first and most critical step in any deployment is ensuring the system is running the latest software. This mitigates risks from known vulnerabilities and ensures that all security tools are compatible with the current kernel.

Unfortunately, my government blocked Kali so I had to use Tsinghua University's kali mirror. Thank you so much Tsinghua!

```bash
# Synchronize package databases and perform a full distribution upgrade
sudo apt update && sudo apt full-upgrade
```

By using `full-upgrade` instead of a simple `upgrade`, we ensure that dependencies are correctly handled, which is vital for a rolling release distribution like Kali.

***

## Section 3: Identity and Credential Management

Default credentials are a primary target for automated scripts and lateral movement. Transitioning the system from the default `kali:kali` setup to a custom user profile (`octal`) provides a layer of obfuscation and better reflects a professional workstation setup.

A unique PIN was generated for the `octal` account. This ensures that even if the machine is exposed to the local lab network, it is not trivial to compromise via SSH or physical access.

***

## Section 4: Localization: Timezone Configuration

In a SOC environment, time is the most important variable. When correlating logs between an attacker machine, a victim machine, and a SIEM, timestamp discrepancies can lead to incorrect conclusions during incident response. Setting the timezone to Yangon ensures all local artifacts align with the physical location of the investigator.

```bash
# Synchronize system time with the local region
sudo timedatectl set-timezone Asia/Yangon

# Verify the system clock and NTP synchronization status
timedatectl
```

***

## Section 5: Desktop Organization and Workflow

Efficiency in the lab is driven by organization. A dedicated workspace prevents "desktop clutter" and ensures that sensitive data like PCAP files, malware samples, and report drafts are stored in a centralized, predictable location.

```bash
# Create the primary lab directory on the Desktop
mkdir -p ~/Desktop/SOC_Lab

# Set permissions to ensure the folder is restricted to the 'octal' user
chmod 700 ~/Desktop/SOC_Lab
```

***

## Section 6: GUI and Environment Customization

The Kali Linux environment was customized to create a more ergonomic and personalized workspace, departing from the default dark aesthetics for better visual clarity.

* **Theme & Aesthetics:** Transitioned from the default dark theme to a **Light Mode** theme. This change was implemented to reduce eye strain during high-detail analysis work and to improve overall interface legibility.
* **Taskbar & Layout:** The primary taskbar was relocated from the top to the **bottom** of the screen to align with a more traditional workstation layout. Additionally, the taskbar and icon sizes were adjusted to provide a cleaner look.
* **Power Management:** Disabled "Screen Blank" and "Auto-suspend." This is essential in a lab setting to prevent power-saving features from interrupting long-running vulnerability scans or forensic processing.
* **Quick Access:** Pinned the core SOC toolset, including Wireshark, Burp Suite, and Nmap to the bottom panel for immediate execution.

Below are attachments of the before and after look of my Kali Linux desktop:

<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

***

## Summary of Progress

Today's session successfully transformed a "stock" Kali installation into a personalized SOC workstation. By hardening the user credentials and standardizing the system time, the lab is now prepared for more advanced tasks.

**Key Achievements:**

* **Virtualization Stability:** Upgraded VM hardware and tools to resolve cursor/input issues and enable multi-monitor support.
* **Hardening:** Removed the default `kali` user identity and updated all system packages.
* **Normalization:** Synchronized system time to **Asia/Yangon** for accurate log correlation.
* **Infrastructure:** Established a structured `SOC_Lab` directory for data management.
* **Persistence:** Configured the desktop environment to support long-running security tasks without session interruption.

With the environment stabilized, the next phase will involve configuring network monitoring and deploying the first set of defensive tools.
