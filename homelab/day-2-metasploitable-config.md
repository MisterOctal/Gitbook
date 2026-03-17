---
icon: server
---

# Day 2 - Metasploitable Config

**Date:** 17.03.2026

**Focus:** Network Isolation, Static IP Allocation, Log Normalization, and Baselining

Day 2 shifts the focus from the attacker workstation (Kali) to the designated victim machine: **Austo-College**. In a SOC Homelab, a deliberately vulnerable machine is necessary to generate malicious traffic, trigger alerts, and provide real-world artifacts for log analysis. However, because it is extremely vulnerable by design, it requires strict containment and specific configurations to be useful for a SIEM.

By the way, Austo-College is a parody of my current college, Auston College!

***

## 1. Absolute Network Containment

Austo-College must never touch a public network or the host machine's primary NAT adapter. If exposed to the internet, it will be compromised within minutes by automated scanners.

* **Hypervisor Configuration:** The VM's network adapter was strictly set to **Host-Only** (`VMnet1`).
* **Validation:** This ensures that Austo-College can communicate with the Kali Linux attacker machine (also on `VMnet1`), but has zero routing capabilities to the outside world or the broader home network.

***

## 2. Static IP Allocation

By default, Austo-College attempts to pull an IP address via DHCP. In a SOC environment, targets and log sources must have predictable, static IP addresses to ensure that firewall rules, SIEM configurations, and attack scripts do not break when the lab is rebooted.

I configured a static IP within the `VMnet1` subnet space.

```bash
# Log in with default credentials (msfadmin:msfadmin)
# Open the network interfaces file
sudo nano /etc/network/interfaces
```

The configuration was updated from `dhcp` to `static`:

```bash
# The primary network interface
auto eth0
iface eth0 inet static
        address 192.168.138.1
        netmask 255.255.255.0
        network 192.168.138.0
        broadcast 192.168.138.255
```

```bash
# Restart the networking service to apply changes
sudo /etc/init.d/networking restart

# Verify the new static IP assignment
ifconfig eth0
```

***

## 3. Timezone Normalization (Legacy OS)

As established in Day 1, chronological alignment is critical for log analysis. Since Austo-College runs on a legacy Ubuntu 8.04 kernel, the system time was synchronized to **Asia/Yangon** using the package reconfiguration tool to ensure the change persists across reboots.

```bash
# Reconfigure the system timezone data
sudo dpkg-reconfigure tzdata

# Selection Path: Asia -> Rangoon

# Verify the timestamp matches the Kali Attacker machine
date
```

This guarantees that when an attack is executed from Kali, the corresponding Apache, SSH, or FTP logs generated on Austo-College will carry the exact same local timestamp, allowing for seamless timeline reconstruction later.

***

## 4. Log Forwarding Preparation (rsyslog)

A SOC analyst needs logs. While we are not deploying the SIEM (Splunk/Elastic) today, the target machine must be prepped to send its internal system and authentication logs over the network.

Austo-College comes with `syslog` out of the box. I verified the service is running and ready to be configured for remote forwarding in a future phase.

```bash
# Check if the syslog daemon is actively writing local logs
sudo tail -f /var/log/auth.log
```

***

## 5. Identity Customization (User Renaming)

To fully transition the machine identity from "Metasploitable" to **Austo-College**, both the hostname and the default user account required manual modification.

```bash
# Edit the hostname file
sudo nano /etc/hostname
# Change 'metasploitable' to 'austo-college'

# Edit the hosts file to prevent sudo lag
sudo nano /etc/hosts
# Update the 127.0.1.1 line to: 127.0.1.1 austo-college
```

The default `msfadmin` account was renamed to `austo`, and the well-known default password was rotated to a lab-specific credential to prevent accidental "low-hanging fruit" access during non-password-based testing.

```bash
# Rename the login name and move the home directory
sudo usermod -l austo -d /home/austo -m msfadmin

# Update the account password to '1111'
sudo passwd austo

# Update the 'Real Name' for the account
sudo chfn -f "Austo College Admin" austo
```

_Note: A system reboot was required after these changes to refresh the `user@hostname` prompt in the terminal._

***

## 6. VM Baselining (The "Golden Image" Snapshot)

Once the static IP, identity, and timezone were locked in, the machine was gracefully shut down.

A hypervisor snapshot was immediately taken and labeled **"Initial Metasploitable Config"**. Because we will be actively exploiting this machine, by dropping malware, breaking services, and altering files. having a pristine, pre-configured snapshot allows the lab to be "reset" in seconds without having to re-do the static IP or timezone configurations.

***

## Summary of Progress

Today’s session successfully integrated the victim node into the "Tug of War" architecture. Austo-College is now a stable, addressable, and chronologically synchronized target.

**Key Achievements:**

* **Infrastructure:** Confirmed Host-Only isolation to protect the physical LAN.
* **Networking:** Replaced DHCP with a permanent Static IP for consistent attack routing.
* **Normalization:** Synchronized legacy system time to **Asia/Yangon** via `tzdata` reconfiguration.
* **Identity:** Rebranded the system hostname and migrated the `msfadmin` account to `austo`.
* **Credential Management:** Rotated the default password to a lab-specific entry (`1111`).
* **Persistence:** Established a baseline snapshot for rapid lab resets.
