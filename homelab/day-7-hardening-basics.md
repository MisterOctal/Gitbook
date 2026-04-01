---
icon: shield
cover: >-
  https://cdn.discordapp.com/attachments/1031890307979149332/1483977967347433593/image.png?ex=69cdb12e&is=69cc5fae&hm=b593a04d4c177f519d67c47eb90a11614bf52a5089ba3e74debeed7899b16240
coverY: 0
coverHeight: 138
---

# Day 7 - Hardening Basics

**Date:** 01.04.2026

**Focus:** Remediation of "The Famous Three" & Aggressive Attack Surface Reduction

Today marks the transition from exploitation to defense. We have moved beyond basic patching into **Full System Hardening**. The goal is to eliminate every unnecessary service and implement a strict firewall policy, reducing the attack surface by over 90%.

***

## Section 1: Patching & Attack Surface Reduction

We are shifting from "fixing bugs" to a **"Deny by Default"** posture.

#### 1. Neutralizing UnrealIRCD (The Backdoor)

* **The Struggle:** `ps aux` showed `/usr/bin/unreal`, but the file was missing.
* **The Solution:** Used `whereis unrealircd` to find the actual binary at `/usr/bin/unrealircd`.
* **Action:** Terminated the process and revoked execution rights.

```bash
sudo kill -9 5209
sudo chmod -x /usr/bin/unrealircd
```

#### 2. Hardening Samba (IP Whitelisting)

Instead of just fixing one script, we are cloaking the entire service from the Kali network.

* **Fix:** Add a strict bind policy to `/etc/samba/smb.conf`.

```bash
# In [global] section:
hosts allow = 127.0.0.1 192.168.138.2
hosts deny = ALL
```

#### 3. Aggressive Firewall (The "Nuclear" Option)

Metasploitable has dozens of legacy ports. Rather than closing them one by one, we will wipe the slate clean.

**The Whitelist Script (Run these in order):**

```bash
# A. CLEAR EXISTING RULES (The Flush)
# Resetting ensures no old rules conflict with our new policy.
sudo iptables -F          # Flush all chains
sudo iptables -X          # Delete custom chains
sudo iptables -Z          # Zero counters
sudo iptables -P INPUT ACCEPT  # Temporarily set policy to ACCEPT to avoid lockout

# B. APPLY NEW WHITELIST
# 1. Allow established connections
# Note: Using the 'state' module for older Linux kernels (Metasploitable 2)
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 2. Allow Loopback (Localhost)
sudo iptables -A INPUT -i lo -j ACCEPT

# 3. Allow Only Essential Services
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT    # SSH
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT    # HTTP
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT   # HTTPS

# 4. DROP EVERYTHING ELSE
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
```

***

## Section 2: Targeted Metasploit Verification

With the firewall active, we use Metasploit's check modules to confirm the "Famous Three" are now unreachable.

#### 1. Verify Samba Isolation

```bash
msf > use exploit/multi/samba/usermap_script
[*] No payload configured, defaulting to cmd/unix/reverse_netcat
msf exploit(multi/samba/usermap_script) > set RHOSTS 192.168.138.100
RHOSTS => 192.168.138.100
msf exploit(multi/samba/usermap_script) > exploit
[*] Started reverse TCP handler on 192.168.244.129:4444 
[-] 192.168.138.100:139 - Exploit failed [unreachable]: Rex::ConnectionTimeout The connection with (192.168.138.100:139) timed out.
[*] Exploit completed, but no session was created.
```

#### 2. Verify Java RMI & Ghost Ports

```bash
nmap -Pn 192.168.138.100                       
Starting Nmap 7.95 ( [https://nmap.org](https://nmap.org) ) at 2026-04-01 16:26 +0630
Nmap scan report for 192.168.138.100
Host is up (0.00048s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE  SERVICE
22/tcp  open   ssh
80/tcp  open   http
443/tcp closed https
MAC Address: 00:0C:29:FA:DD:34 (VMware)

Nmap done: 1 IP address (1 host up) scanned in 11.47 seconds
```

***

## Section 3: The Checklist

| Service          | Port    | Remediation      | Status         |
| ---------------- | ------- | ---------------- | -------------- |
| **UnrealIRCD**   | 6667    | Binary Disabled  | **REMEDIATED** |
| **Samba**        | 139/445 | IP Whitelist     | **CLOAKED**    |
| **Java RMI**     | 1099    | Default Drop     | **BLOCKED**    |
| **NFS/RPC**      | 2049    | Default Drop     | **BLOCKED**    |
| **R-Services**   | 512-514 | Default Drop     | **BLOCKED**    |
| **Backdoor FTP** | 21      | Service Disabled | **REMOVED**    |

***

## Summary of Progress

* **Reduced Attack Surface:** Successfully limited external entry points from dozens of legacy ports down to just three essential services.
* **Closed Identified Gaps:** Verified that the specific exploits used previously are no longer effective from the external network.
* **Initial Hardening Baseline:** Established a foundational security posture that makes the system a significantly more difficult target than it was in its default state.
* **Ongoing Effort:** Recognized that while the "low-hanging fruit" vulnerabilities are resolved, security is a continuous process that requires further monitoring and regular updates.

**Note:** A copy of the firewall config has been pushed onto GitHub as well.

**Conclusion:** We have transformed Austo-College from a vulnerable lab into a hardened environment. By implementing a default-drop firewall policy, we have mitigated not only the known vulnerabilities but also any "Zero-Day" exploits targeting the remaining legacy services.
