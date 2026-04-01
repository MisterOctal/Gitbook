---
icon: magnifying-glass
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1721904048318.png
coverY: 0
coverHeight: 138
---

# Tcpdump: The Basics

**Date:** 23.03.2026

**Room Category:** Walkthrough

**Core Concept:** Mastering command-line packet capture and analysis using Tcpdump and the libpcap library.

The main challenge when studying networking protocols is that we don’t get a chance to see the protocol “conversations” taking place. All the technical complexities are hidden behind friendly user interfaces. This room introduces the foundational command-line arguments for Tcpdump, a stable and high-speed tool released for Unix-like systems in the late 80s. Learning to capture and filter traffic at the CLI is a critical skill for any security analyst.

***

## Task 1: Introduction

Tcpdump and its `libpcap` library are written in C/C++. The `libpcap` library is the foundation for various other networking tools today and was even ported to Windows as `winpcap`.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What is the name of the library that is associated with tcpdump?

> **Answer:** libpcap

***

## Task 2: Basic Packet Capture

When running tcpdump, we must be specific about the interface, the count, and the formatting.

**Key Commands:**

* `-i [interface]`: Specify the network interface (e.g., `eth0` or `any`).
* `-w [file]`: Write captured packets to a `.pcap` file for later analysis.
* `-r [file]`: Read packets from a saved file.
* `-c [count]`: Limit the number of packets captured.
* `-n`: Don't resolve IP addresses to hostnames.
* `-nn`: Don't resolve IP addresses OR port numbers (e.g., show `80` instead of `http`).
* `-v`, `-vv`, `-vvv`: Increase the level of verbosity in the output.

Question: What option can you add to your command to display addresses only in numeric format?

> **Answer:** -n

***

## Task 3: Filtering Expressions

Filtering allows us to ignore the "noise" and focus on specific conversations.

* **Filtering by Host:** `host 10.10.10.1` or `src host [IP]` / `dst host [IP]`.
* **Filtering by Port:** `port 53` (DNS), `port 80` (HTTP), etc.
* **Filtering by Protocol:** `icmp`, `tcp`, `udp`, `arp`, `ip`.
* **Logical Operators:** `and`, `or`, and `not`.

Question: How many packets in traffic.pcap use the ICMP protocol?

_Command: `tcpdump -r traffic.pcap icmp -n | wc -l`_

> **Answer:** 26

Question: What is the IP address of the host that asked for the MAC address of 192.168.124.137?

_Command: `tcpdump -r traffic.pcap arp -n`_

> **Answer:** 192.168.124.148

Question: What hostname (subdomain) appears in the first DNS query?&#x20;

_Command: `tcpdump -r traffic.pcap udp port 53 -n -c 1`_

> **Answer:** mirrors.rockylinux.org

***

## Task 4: Advanced Filtering

Advanced filtering uses binary operations and header offsets to find specific data within a packet.

**Binary Operators:**

* `&` (And): Both bits must be 1.
* `|` (Or): Either bit can be 1.
* `!` (Not): Inverts the bit.

**TCP Flags:**

* `tcp[tcpflags] == tcp-syn`: Capture only SYN packets.
* `tcp[tcpflags] & tcp-syn != 0`: Capture packets with at least the SYN flag set.

Question: How many packets have only the TCP Reset (RST) flag set?&#x20;

_Command: `tcpdump -r traffic.pcap "tcp[tcpflags] == tcp-rst" -n | wc -l`_

> **Answer:** 57

Question: What is the IP address of the host that sent packets larger than 15000 bytes?&#x20;

_Command: `tcpdump -r traffic.pcap greater 15000 -n`_

> **Answer:** 185.117.80.53

***

## Task 5: Displaying Packets

We can customize how the data inside the packets is printed to the terminal.

* `-q`: Quick/brief output.
* `-e`: Print link-level headers (includes MAC addresses).
* `-A`: Display data in ASCII (readable text).
* `-xx`: Display data in Hexadecimal.
* `-X`: Display data in both Hex and ASCII.

Question: What is the MAC address of the host that sent an ARP request?&#x20;

_Command: `tcpdump -r traffic.pcap arp -e`_

> **Answer:** 52:54:00:7c:d3:5b

***

## Conclusion

Tcpdump is an excellent tool for understanding protocols at a deep level. While tools like Wireshark provide a GUI, Tcpdump is the king of quick, remote, and scriptable network analysis.
