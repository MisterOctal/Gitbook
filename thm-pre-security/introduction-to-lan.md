---
icon: chart-network
---

# Introduction to LAN

Date: 01.03.2026

Room Category: Theory

Core Concept: Local Area Networks and core network protocols

Understanding how networks operate at a fundamental level is super important. Before we start attacking or defending networks, we need to know how devices actually talk to each other in the same room or building! This room is heavily theory-based but covers essential concepts like topologies, subnetting, and core protocols.

Also, funnily enough, just a few days ago I was finishing up my HNC report which covers this stuff. I had to defend why I wanted the company in the scenario to use a hierarchal star topology.

***

## Task 1: Introducing LAN Topologies

A LAN (Local Area Network) is a network found in a single geographical location, like a house, a small business, or an office. It's the foundation of how devices connect locally before routing out to the wider internet.

Question: What does LAN stand for?

> Answer: Local Area Network

What is the verb given to the job that Routers perform?

> Answer: Routing

Question: What device is used to centrally connect multiple devices on the local network and transmit data to the correct location?

> Answer: Switch

In this task, we also learn about the different ways a network can be physically or logically arranged, which is called a topology. The room covers a few main ones:

<figure><img src="../.gitbook/assets/image (11).png" alt="" width="425"><figcaption></figcaption></figure>

* **Star Topology:** The most common one. All devices connect to a central switch. It's robust because if one cable breaks, only that device goes offline.

<figure><img src="../.gitbook/assets/image (12).png" alt="" width="375"><figcaption></figcaption></figure>

* **Bus Topology:** All devices share a single backbone cable. It's cheap but if the main cable breaks, the whole network goes down.

<figure><img src="../.gitbook/assets/image (13).png" alt="" width="375"><figcaption></figcaption></figure>

* **Ring Topology:** Devices are connected in a circle. Data travels in one direction, but it's pretty outdated nowadays.

Question: What topology is cost-efficient to set up?

> Answer: Bus Topology

Question: What topology is expensive to set up and maintain?

> Answer: Star Topology

Lastly, we can also access an interactive website to explore the flaws of topologies. The site is pretty straightforward and walks you through it. After going through all the flaws, you rceive a flag.

Question:

> Answer: THM{TOPOLOGY\_FLAWS}

***

## Task 2: A Primer on Subnetting

Subnetting is basically taking a massive network and carving it up into smaller, more manageable mini-networks. This helps with security, organization, and making sure the network isn't congested with unnecessary traffic. It's a huge topic, but this task just touches on the basics of IPv4 addresses, which are made up of four octets (32 bits total).

Subnetting was also a huge part of my HNC networking syllabus, we had to ensure that all the departments in the given scenario were given dedicated subnets.

<figure><img src="../.gitbook/assets/image (14).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the technical term for dividing a network up into smaller pieces?

> Answer: Subnetting

Question: How many bits are in an IPv4 address?

> Answer: 32

Question: What is the range of a section (octet) of a subnet mask?

> Answer: 0-255

Question: What address is used to identify the start of a network?

> Answer: Network Address

Question: What address is used to identify devices within a network?

> Answer: Host Address

Question: What is the name used to identify the device responsible for sending data to another network?

> Answer: Default Gateway

***

## Task 3: ARP

ARP stands for Address Resolution Protocol. It's the technology that allows a device to link an IP address to a physical MAC address. Devices need MAC addresses to communicate on a local network, so if a computer only knows the target's IP, it sends out an "ARP Request" broadcasting to everyone asking, "Who has this IP?" and the owner replies with their MAC address.

<figure><img src="../.gitbook/assets/image (15).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What does ARP stand for?

> Answer: Address Resolution Protocol

Question: What category of ARP Packet asks a device whether or not it has a specific IP address?

> Answer: Request

Question: What address is used as a physical identifier for a device on a network?

> Answer: MAC Address

Question: What address is used as a logical identifier for a device on a network?

> Answer: IP Address

***

## Task 4: DHCP

DHCP is a lifesaver. Instead of manually typing in an IP address, subnet mask, and gateway for every single phone and computer connecting to a network, DHCP does it automatically. When a device joins, it sends a DHCP Discover packet to find the DHCP server, which then offers it an available IP address to use.

<figure><img src="../.gitbook/assets/image (16).png" alt="" width="477"><figcaption></figcaption></figure>

Question: What type of DHCP packet is used by a device to retrieve an IP address?

> Answer: DHCP Discover

Question: What type of DHCP packet does a device send once it has been offered an IP address by the DHCP server?

> Answer: DHCP Request

Question: Finally, what is the last DHCP packet that is sent to a device from a DHCP server?

> Answer: DHCP ACK

***

## Conclusion

This room was a lot of theory, but these are the foundational building blocks of IT and cybersecurity. Subnetting, ARP, and DHCP are things you see constantly when analyzing packet captures or setting up environments. It's also great review material for my Security+ later this year!

That's all for this room, see you around!
