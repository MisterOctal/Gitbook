---
icon: chart-network
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1721904048318.png
coverY: 0
coverHeight: 138
---

# Networking Concept

**Date:** 22.03.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the OSI and TCP/IP models, IP addressing, subnetting, and basic network communication protocols.

Understanding how a packet travels from its source to its destination is the foundation of both offensive and defensive security. Without these basics, trying to secure a network is like trying to fix an engine without knowing how the fuel reaches the cylinders.

***

## Task 1: Introduction

Networking is the backbone of the internet. This room serves as the starting point for four modules that cover everything from basic connectivity to secure protocols. By the end of this, I will have a better understanding of how data is structured and moved across different hardware and software layers.

Question: Get your notepad ready, and let’s begin.

> **Answer:** No answer needed

***

## Task 2: OSI Model

The OSI (Open Systems Interconnection) model is a conceptual framework that describes how communications should occur in a computer network. Even though it is a theoretical model, it is vital to learn because it helps me visualize where a problem might be occurring during troubleshooting or an attack. The model consists of seven distinct layers, ranging from the physical hardware to the application I am currently using.

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Physical Layer:** Hardware transmission like cables and switches.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

* **Data Link Layer:** Transferring data between nodes on the same network.

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1719848867222.svg" alt="" width="563"><figcaption></figcaption></figure>

* **Network Layer:** Routing packets across different networks using IP addresses.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Transport Layer:** Managing data transfer reliability via TCP or UDP.
* **Session, Presentation, and Application Layers:** Handling the higher level functions like encryption and the actual software interface.

Question: Which layer is responsible for end-to-end communication between running applications?

> **Answer:** 4

Question: Which layer is responsible for routing packets to the proper network?

> **Answer:** 3

Question: In the OSI model, which layer is responsible for encoding the application data?

> **Answer:** 6

Question: Which layer is responsible for transferring data between hosts on the same network segment?

> **Answer:** 2

***

## Task 3: TCP/IP Model

While the OSI model is great for theory, the TCP/IP model is what the internet actually uses. It is a more condensed version with only four layers: Link, Internet, Transport, and Application. It simplifies the process by grouping the top three layers of the OSI model into a single Application layer.

Question: To which layer does HTTP belong in the TCP/IP model?

> **Answer:** Application Layer

Question: How many layers of the OSI model does the application layer in the TCP/IP model cover?

> **Answer:** 3

***

## Task 4: IP Addresses and Subnets

Every host on a network needs a unique identifier so other hosts can find it. This is where IP addresses come in. In this room, we focus on IPv4, which are 32-bit addresses divided into four octets. Subnetting is another crucial concept, it allows us to divide a large network into smaller, more manageable ones for better security and performance.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

#### Looking Up Your Network Configuration

When you want to find my IP address on a Windows machine, you use `ipconfig`. On Linux, you can use `ifconfig` or the more modern `ip a s`. Seeing this in the terminal helps you identify your position in the network.

```bash
user@TryHackMe$ ifconfig
[...]
wlo1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.66.89  netmask 255.255.255.0  broadcast 192.168.66.255
        inet6 fe80::73e1:ca5e:3f93:b1b3  prefixlen 64  scopeid 0x20<link>
        ether cc:5e:f8:02:21:a7  txqueuelen 1000  (Ethernet)
```

The output above confirms that the laptop IP is `192.168.66.89` with a subnet mask of `255.255.255.0`. you can also use `ip a s` to see the same information presented slightly differently:

```bash
user@TryHackMe$ ip a s
[...]
4: wlo1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether cc:5e:f8:02:21:a7 brd ff:ff:ff:ff:ff:ff
    altname wlp3s0
    inet 192.168.66.89/24 brd 192.168.66.255 scope global dynamic noprefixroute wlo1
```

Here, the subnet mask is shown as `/24`, which is just a shorthand for `255.255.255.0`. It tells me the first 24 bits (three octets) stay the same for everyone on this subnet.

Question: Which of the following IP addresses is not a private IP address?

* 192.168.250.125
* 10.20.141.132
* 49.69.147.197
* 172.23.182.251

> **Answer:** 49.69.147.197

Question: Which of the following IP addresses is not a valid IP address?

* 192.168.250.15
* 192.168.254.17
* 192.168.305.19
* 192.168.199.13

> **Answer:** 192.168.305.19

***

## Task 5: UDP and TCP

The IP protocol gets us to the right house, but TCP and UDP get us to the right room. These transport protocols allow different processes on a host to communicate.

* **UDP (User Datagram Protocol):** This is the "fast" one. It does not care if the data arrives or not, making it perfect for video streaming or gaming where speed is more important than perfect accuracy.
* **TCP (Transmission Control Protocol):** This is the "reliable" one. It uses a three-way handshake (SYN, SYN-ACK, ACK) to ensure a connection is established before sending data.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Which protocol requires a three-way handshake?

> **Answer:** TCP

Question: What is the approximate number of port numbers (in thousands)?

> **Answer:** 65

***

## Task 6: Encapsulation

Encapsulation is the process of wrapping data in "envelopes" as it moves down the network layers. It starts as raw application data, gets a header at the transport layer to become a **segment** or **datagram**, gets an IP header at the network layer to become a **packet**, and finally gets a header and trailer at the link layer to become a **frame**. This process is reversed on the receiving end, which is known as de-encapsulation.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: On a WiFi, within what will an IP packet be encapsulated?

> **Answer:** Frame

Question: What do you call the UDP data unit that encapsulates the application data?

> **Answer:** Datagram

Question: What do you call the data unit that encapsulates the application data sent over TCP?

> **Answer:** Segment

***

## Task 7: Telnet

Telnet is an old school command line tool used to talk to remote services. While it is insecure because it sends data in cleartext, it is a fantastic tool for testing if a port is open or seeing how a service responds to manual commands.

Question: Use telnet to connect to the web server on 10.49.135.116 What is the name and version of the HTTP server?

> **Answer:** lighttpd/1.4.63

Question: What flag did you get when you viewed the page?

> **Answer:** THM{TELNET\_MASTER}

***

## Conclusion

This room provided a solid foundation for how computers talk to each other. Understanding the difference between a packet and a frame, or why we use TCP instead of UDP for certain tasks, is essential for anyone looking to work in a SOC or as a pentester. Networking isn't just a prerequisite, it is the environment where all our security work takes place.
