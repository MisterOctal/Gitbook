---
icon: magnifying-glass
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/6131132af49360005df01ae3/room-content/10f7d168ab59410ddc28a4b1e89fd6d4.png
coverY: 0
coverHeight: 138
---

# Wireshark: The Basics

**Date:** 23.03.2026

**Room Category:** Walkthrough

**Core Concept:** Introduction to network traffic analysis, packet dissection, and using Wireshark to investigate protocol details and security anomalies.

Wireshark is the world’s most widely used network protocol analyzer. It lets you see what’s happening on your network at a microscopic level. For a security professional, it is an indispensable tool for troubleshooting network issues, identifying malicious traffic patterns, and verifying that encryption is working as intended. This room serves as the entry point for turning raw network data into actionable intelligence.

***

## Task 1: Introduction

Wireshark is an open-source tool used to "sniff" live traffic or inspect pre-recorded packet captures (PCAPs). Before diving in, it's recommended to have a solid grasp of the OSI model, as Wireshark organizes data based on those layers.

Question: Which file is used to simulate the screenshots?

> **Answer:** http1.pcapng

Question: Which file is used to answer the questions?

> **Answer:** Exercise.pcapng

***

## Task 2: Tool Overview

The Wireshark interface is divided into several key panes:

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Packet List:** A top-level summary of every packet (Source, Destination, Protocol).
* **Packet Details:** A breakdown of the selected packet by OSI layers.
* **Packet Bytes:** The raw hex and ASCII representation of the data.

**Key Features:**

* **Sniffing:** Begins the sniffing and packet collection when the shark button is pressed.
* **Coloring Rules:** Helps visually distinguish protocols (e.g., HTTP is usually green, TCP errors are black/red).

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Merge:** Combining multiple capture files into one for a unified timeline.
* **Capture File Properties:** Viewing metadata like hashes, capture time, and OS information.

Question: Read the "capture file comments". What is the flag?

> **Answer:** TryHackMe\_Wireshark\_Demo

Question: What is the total number of packets?

> **Answer:** 58620

Question: What is the SHA256 hash value of the capture file?

> **Answer:** f446de335565fb0b0ee5e5a3266703c778b2f3dfad7efeaeccb2da5641a6d6eb

***

## Task 3: Packet Dissection

Packet dissection is the process of decoding the bits and bytes into readable protocol fields. Wireshark maps these directly to the OSI model:

* **Frame (Layer 1):** Physical details.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Ethernet II (Layer 2):** MAC addresses.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Internet Protocol (Layer 3):** IP addresses and TTL.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Transmission Control Protocol (Layer 4):** Ports and sequence numbers.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Application Layer (Layer 5-7):** HTTP headers, data payloads, etc.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>



Question: View packet number 38. Which markup language is used under the HTTP protocol?

> **Answer:** eXtensible Markup Language

Question: What is the arrival date of the packet? (Answer format: Month/Day/Year)

> **Answer:** 05/13/2004

Question: What is the TTL value?

> **Answer:** 47

Question: What is the TCP payload size?

> **Answer:** 424

Question: What is the e-tag value?

> **Answer:** 9a01a-4696-7e354b00

***

## Task 4: Packet Navigation

Efficient navigation is crucial when dealing with thousands of packets. You can search for specific strings, go to a specific packet number, mark packets for later, or export specific objects (like images or files) that were transferred over the network.

**Expert Info:** This feature highlights potential issues like TCP retransmissions or malformed packets, categorized by severity (Chat, Note, Warn, Error).

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: Search the "r4w" string in packet details. What is the name of artist 1?

> **Answer:** r4w8173

Question: Go to packet 12 and read the comments. What is the answer?

> **Answer:** 911cd574a42865a956ccde2d04495ebf

Question: There is a ".txt" file inside the capture file. Find the file and read it; what is the alien's name?

> **Answer:** PACKETMASTER

Question: Look at the expert info section. What is the number of warnings?

> **Answer:** 1636

***

## Task 5: Packet Filtering

Filtering is the most powerful part of Wireshark.

* **Apply as Filter:** Right-clicking a field to instantly isolate that value.
* **Follow Stream:** Reconstructing a conversation (TCP/UDP/HTTP) to see the raw data as the application saw it (e.g., a full web page or a chat log).

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: Go to packet number 4. Right-click on the "Hypertext Transfer Protocol" and apply it as a filter. Now, look at the filter pane. What is the filter query?

> **Answer:** http

Question: What is the number of displayed packets?

> **Answer:** 1089

Question: Go to packet number 33790 and follow the stream. What is the total number of artists?

> **Answer:** 3

Question: What is the name of the second artist?

> **Answer:** Blad3

***

## Conclusion

Understanding the basics of Wireshark allows us to peel back the layers of network communication. By mastering navigation, dissection, and filtering, we can move from simply seeing traffic to actually analyzing the intent and content of network behavior. This is a foundational skill for any security analyst or network engineer.
