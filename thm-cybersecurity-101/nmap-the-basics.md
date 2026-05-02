---
icon: eye
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1721904048318.png
coverY: 0
coverHeight: 139
---

# Nmap: The Basics

**Date:** 23.03.2026

**Room Category:** Walkthrough

**Core Concept:** Using Nmap for host discovery, port scanning, service version detection, and OS fingerprinting.

Nmap (Network Mapper) is an open-source tool for network exploration and security auditing. It was designed to rapidly scan large networks, although it works fine against single hosts. In a security context, Nmap is the industry standard for the "Enumeration" phase, allowing an attacker or auditor to map out the attack surface by identifying live hosts and the services they expose to the network.

***

## Task 1: Introduction

Manual discovery (using `ping` or `telnet`) is inefficient for large networks. Nmap automates this process by sending crafted packets and analyzing the responses. This room covers the foundational commands needed to perform basic reconnaissance.

Question: It’s time to find out who is listening on the network.

> **Answer:** No answer needed

***

## Task 2: Host Discovery: Who Is Online

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Before scanning for ports, we need to know which IP addresses are actually active. This is "Host Discovery."

* **Scan Ranges:** You can scan specific ranges (`192.168.0.1-10`), subnets (`192.168.0.1/24`), or hostnames (`example.thm`).
* **Ping Scan (`-sn`):** This tells Nmap not to scan ports after discovery.
  * On a **Local Network**, Nmap uses ARP requests to find hosts.
  * On a **Remote Network**, Nmap uses ICMP echo requests, TCP ACK to port 80, TCP SYN to port 443, and ICMP timestamp requests.
* **List Scan (`-sL`):** This simply lists the targets Nmap _would_ scan without sending any packets to them.

Question: What is the last IP address that will be scanned when your scan target is 192.168.0.1/27?

> **Answer:** 192.168.0.31

***

## Task 3: Port Scanning: Who Is Listening

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Once a host is found to be "up," we check its ports (0-65535) to see which services are running.

<table data-header-hidden><thead><tr><th width="97.800048828125"></th><th width="180.199951171875"></th><th></th></tr></thead><tbody><tr><td>Option</td><td>Name</td><td>Description</td></tr><tr><td><code>-sT</code></td><td><strong>TCP Connect Scan</strong></td><td>Completes the 3-way handshake. Used when the user doesn't have raw packet privileges (non-root).</td></tr><tr><td><code>-sS</code></td><td><strong>SYN Scan</strong></td><td>"Stealth" scan. Sends a SYN but resets (RST) before completing the handshake. Fast and less likely to be logged.</td></tr><tr><td><code>-sU</code></td><td><strong>UDP Scan</strong></td><td>Scans for UDP services (DNS, DHCP, SNMP). Slower because UDP doesn't guarantee a response.</td></tr><tr><td><code>-F</code></td><td><strong>Fast Mode</strong></td><td>Scans only the top 100 most common ports.</td></tr><tr><td><code>-p-</code></td><td><strong>All Ports</strong></td><td>Scans all 65,535 ports.</td></tr></tbody></table>

Question: How many TCP ports are open on the target system at MACHINE\_IP?

> **Answer:** 6

Question: What is the flag that appears on the main page of the listening web server?

> **Answer:** THM{SECRET\_PAGE\_38B9P6}

***

## Task 4: Version Detection: Extract More Information

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Knowing a port is open isn't enough; we need to know the specific software and version to find applicable exploits.

* **Service Versioning (`-sV`):** Probes open ports to determine what software (and version) is running.
* **OS Detection (`-O`):** Analyzes packet responses to guess the Operating System.
* **Aggressive Scan (`-A`):** A "combo" flag that enables OS detection, version detection, script scanning, and traceroute.
* **No Ping (`-Pn`):** Skips host discovery. Useful if a target blocks ICMP (ping) but has open ports.

Question: What is the name and detected version of the web server running on MACHINE\_IP?

> **Answer:** lighttpd 1.4.74

***

## Task 5: Timing: How Fast is Fast

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1723711770591.svg" alt="" width="563"><figcaption></figcaption></figure>

Nmap allows you to control the speed of the scan to avoid crashing services or triggering Intrusion Detection Systems (IDS).

* **Timing Templates (`-T<0-5>`):**
  * `T0/T1`: Paranoid/Sneaky (Very slow, avoids detection).
  * `T2`: Polite.
  * `T3`: Normal (Default).
  * `T4`: Aggressive (Fast, usually safe on modern networks).
  * `T5`: Insane (Extremely fast, may miss ports or crash services).

Question: What is the non-numeric equivalent of -T4?

> **Answer:** aggressive

***

## Task 6: Output: Controlling What You See

Documentation is a key part of any security assessment. Nmap can save results in several formats:

* `-oN`: Normal output (what you see on screen).
* `-oG`: Grepable output (easy to search with command-line tools).
* `-oX`: XML output (useful for importing into other tools like Zenmap or Metasploit).
* `-oA`: Output in **all** three formats simultaneously.
* `-v / -vv`: Increase verbosity (see results in real-time).
* `-d`: Debugging mode for troubleshooting.

Question: What option must you add to your nmap command to enable debugging?

> **Answer:** -d

***

## Conclusion

Nmap's behavior changes based on your user privileges. If run as **root/sudo**, it defaults to a **SYN Scan (-sS)**. If run as a **regular user**, it defaults to a **Connect Scan (-sT)** because regular users cannot craft raw packets.

Question: What kind of scan will Nmap use if you run nmap MACHINE\_IP with local user privileges?

> **Answer:** Connect Scan

Below is a consolidated reference of the syntax and flags learned in this room.

<table data-header-hidden><thead><tr><th width="120.20001220703125"></th><th width="127.4000244140625"></th><th></th></tr></thead><tbody><tr><td>Category</td><td>Flag</td><td>Description</td></tr><tr><td><strong>Discovery</strong></td><td><code>-sL</code></td><td><strong>List Scan:</strong> Lists targets without sending packets.</td></tr><tr><td></td><td><code>-sn</code></td><td><strong>Ping Scan:</strong> Host discovery only (no port scan).</td></tr><tr><td></td><td><code>-Pn</code></td><td><strong>No Ping:</strong> Skips discovery; treats all hosts as online.</td></tr><tr><td><strong>Scanning</strong></td><td><code>-sS</code></td><td><strong>SYN Scan:</strong> Stealthy TCP scan (requires root).</td></tr><tr><td></td><td><code>-sT</code></td><td><strong>Connect Scan:</strong> Full TCP handshake (default for non-root).</td></tr><tr><td></td><td><code>-sU</code></td><td><strong>UDP Scan:</strong> Scans for UDP services.</td></tr><tr><td><strong>Targeting</strong></td><td><code>-p &#x3C;port></code></td><td>Scan specific port(s). Use <code>-p-</code> for all 65k ports.</td></tr><tr><td></td><td><code>-F</code></td><td><strong>Fast Mode:</strong> Scans top 100 most common ports.</td></tr><tr><td><strong>Detection</strong></td><td><code>-sV</code></td><td><strong>Service Detection:</strong> Identifies software versions.</td></tr><tr><td></td><td><code>-O</code></td><td><strong>OS Detection:</strong> Identifies the operating system.</td></tr><tr><td></td><td><code>-A</code></td><td><strong>Aggressive:</strong> OS, Version, Scripts, and Traceroute.</td></tr><tr><td><strong>Timing</strong></td><td><code>-T&#x3C;0-5></code></td><td>Set timing template (0=Paranoid, 5=Insane).</td></tr><tr><td><strong>Output</strong></td><td><code>-v</code></td><td><strong>Verbosity:</strong> Shows more detail during the scan.</td></tr><tr><td></td><td><code>-oA &#x3C;file></code></td><td><strong>All Formats:</strong> Saves in Normal, XML, and Grepable.</td></tr><tr><td></td><td><code>-d</code></td><td><strong>Debug:</strong> Enables debugging output.</td></tr></tbody></table>

This room provides the essential toolkit for network enumeration. Mastering Nmap flags is not just about memorization, but about understanding the underlying TCP/IP mechanics of how hosts and services respond to different types of probes.

