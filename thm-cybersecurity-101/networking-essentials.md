---
icon: chart-network
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1721904048318.png
coverY: 0
coverHeight: 138
---

# Networking Essentials

**Date:** 22.03.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the essential protocols that glue a network together, including DHCP, ARP, NAT, and diagnostic tools like Ping and Traceroute.

This room is the second in our series, moving from theoretical models to the practical protocols, DHCP, ARP, and NAT that make modern connectivity possible. Mastering these "glue" protocols is a prerequisite for any security professional, as they represent the foundation of a functional network.

***

## Task 1: Introduction

Networking is the backbone of the digital world. While the previous room covered the "layers," this room focuses on the services that populate those layers. We will explore how devices find each other, how they get their addresses, and how we troubleshoot connections when they fail.

Question: Get your notepad ready, and let’s begin.

> **Answer:** No answer needed

***

## Task 2: DHCP: Give Me My Network Settings

DHCP automates the network configuration process. Instead of manually typing in an IP, subnet mask, and gateway for every device, a DHCP server "leases" this information to clients automatically. This is why your phone can join a coffee shop Wi-Fi and just _work_.

**The DORA Process:**

1. **Discover:** The client broadcasts to find a DHCP server.
2. **Offer:** The server responds with an available IP.
3. **Request:** The client asks to use that specific IP.
4. **Acknowledge (ACK):** The server confirms the lease.

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: How many steps does DHCP use to provide network configuration?

> **Answer:** 4

Question: What is the destination IP address that a client uses when it sends a DHCP Discover packet?

> **Answer:** 255.255.255.255

Question: What is the source IP address a client uses when trying to get IP network configuration over DHCP?

> **Answer:** 0.0.0.0

***

## Task 3: Bridging Layer 3 Addressing to Layer 2 Addressing

While IP addresses get data to the right network, **MAC addresses** get data to the right physical hardware on a local link. ARP bridges the gap by mapping a Layer 3 IP address to a Layer 2 MAC address. When your computer knows the IP but not the hardware address, it sends an ARP Request: _"Who has 192.168.1.1? Tell me your MAC!"_

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the destination MAC address used in an ARP Request?

> **Answer:** ff:ff:ff:ff:ff:ff

Question: In the example provided in the room, what is the MAC address of 192.168.66.1?

> **Answer:** 44:df:65:d8:fe:6c

***

## Task 4: ICMP: Troubleshooting Networks

ICMP is the language of network diagnostics. It isn't used to send user data, but rather to share status and error messages between devices.

* **Ping:** Uses ICMP Echo Requests (Type 8) and Replies (Type 0) to check if a host is alive.
* **Traceroute:** Uses the **TTL (Time-to-Live)** field in the IP header. By intentionally setting the TTL to 1, 2, 3, etc., it forces routers along the path to drop the packet and send back an ICMP "Time Exceeded" message, revealing their identity.

Below are images of wireshark during the ping process:

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Using the example images, how many bytes were sent in the echo (ping) request?

> **Answer:** 40

Question: Which IP header field does the traceroute command require to become zero?

> **Answer:** TTL

***

## Task 5: Routing

Routing protocols are the sets of rules that routers use to determine the most efficient path for data to travel across a network. Without these protocols, routers wouldn't know where to send packets destined for networks they aren't directly connected to.

<p align="center"><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1719849271800.svg" alt="Three networks are connected to the Internet through its own router."></p>



* **OSPF (Open Shortest Path First):** A widely used link-state protocol for interior networks (IGP). It builds a complete map of the network topology and uses the Shortest Path First (SPF) algorithm to find the fastest route.
* **BGP (Border Gateway Protocol):** Known as the "Protocol of the Internet." It is an exterior gateway protocol (EGP) used to exchange routing information between different Autonomous Systems (AS), such as large ISPs.
* **EIGRP (Enhanced Interior Gateway Routing Protocol):** An advanced distance-vector protocol developed by Cisco. It is "proprietary," meaning it was designed specifically for Cisco hardware, though some parts have since been opened up. It uses a mix of bandwidth and delay to calculate the best path.
* **RIP (Routing Information Protocol):** One of the oldest protocols. It uses a simple "hop count" metric (max 15 hops), making it easy to configure but inefficient for large, complex networks.

Question: Which routing protocol discussed in this task is a Cisco proprietary protocol?

> **Answer:** EIGRP

***

## Task 6: NAT (Network Address Translation)

NAT is a survival mechanism for IPv4. Since there aren't enough public IP addresses for every device on Earth, NAT allows an entire private network (like your home) to hide behind a single public IP address. The router tracks internal requests and maps them to external ports, acting as a middleman for all traffic.

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: In the network diagram, what is the public IP that the phone will appear to use when accessing the Internet?

> **Answer:** 212.3.4.5

Question: Assuming the router has infinite power, approximately how many thousand simultaneous TCP connections can it maintain?

> **Answer:** 65

***

## Conclusion

We have now covered the essential "glue" of the network. From DHCP assigning our identity to ARP finding our neighbors and NAT allowing us to reach the wider world, these protocols are the silent workhorses of every network. Understanding these flows is the first step toward identifying anomalies and securing complex environments.

Question: Click on the View Site button and follow the instructions to obtain the flag.

> **Answer:** THM{computer\_is\_happy}
