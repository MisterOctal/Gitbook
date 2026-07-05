---
icon: pickaxe
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/6131132af49360005df01ae3/room-content/8d849ce530319fa09108b97f1c0d3c5f.png
coverY: 0
coverHeight: 139
---

# NetworkMiner

**Date:** 22.04.2026

**Room Category:** Walkthrough

**Core Concept:** Using NetworkMiner as a Network Forensic Analysis Tool (NFAT) to identify hosts, extract files, and uncover credentials from traffic captures.

NetworkMiner is a powerful, open source forensic tool that approaches traffic analysis differently than Wireshark. While Wireshark is excellent for deep packet inspection and protocol decoding, NetworkMiner is built to reconstruct the "objects" within the traffic. It automatically organizes data into categories like hosts, files, images, and credentials, making it my go-to tool for grabbing the low hanging fruit in a network investigation.

***

## Task 1: Room Introduction

NetworkMiner serves as a passive network sniffer and PCAP handler. It is particularly popular among incident response teams because it can regenerate transmitted files and certificates directly from the wire or a capture file. In this room, we will explore how to navigate its interface to conduct efficient network forensics.

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: Read the task above.

> **Answer:** No answer needed

***

## Task 2: NetworkMiner in Forensics

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The goal of network forensics is to detect anomalies and security breaches. NetworkMiner assists by providing immediate context on captured hosts, such as their IP/MAC addresses and OS information. It can also highlight potential attack indicators like traffic spikes or the use of scanning tools like Nmap.

Question: Read the task above.

> **Answer:** No answer needed

***

## Task 3: What is NetworkMiner?

NetworkMiner has several core capabilities, including traffic sniffing, PCAP parsing, and OS fingerprinting. One of its strongest features is the ability to extract images, HTML files, and emails without needing to manually follow TCP streams.

It is important to remember that NetworkMiner is an NFAT, not a dedicated sniffer like tcpdump. While it can capture live traffic on Windows, its primary strength lies in the offline analysis of recorded traffic.

Question: Read the task above.

> **Answer:** No answer needed

***

## Task 4: Tool Overview 1

The interface is divided into several intuitive tabs. The **Hosts** tab aggregates everything known about a specific IP, while the **Sessions** tab shows the flow of communication. The **Credentials** tab is especially useful for quickly finding cleartext passwords or hashes like Kerberos and NTLM without having to search through raw packets.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: What is the total number of frames? (mx-3.pcap)

> **Answer:** 460

Question: How many IP addresses use the same MAC address with host 145.253.2.203?

> **Answer:** 2

Question: How many packets were sent from host 65.208.228.223?

> **Answer:** 72

Question: What is the name of the webserver banner under host 65.208.228.223?

> **Answer:** Apache

Question: What is the extracted username for the 02694W-WIN10 host? (mx-4.pcap)

> **Answer:** #B\Administrator

Question: What is the extracted password for the user logged into the 02694W-WIN10 host? Enter the full NTLM hash.

> **Answer:** $NETNTLMv2$#B$136B077D942D9A63$FBFF3C253926907AAAAD670A9037F2A5$01010000000000000094D71AE38CD60170A8D571127AE49E00000000020004003300420001001E003000310035003600360053002D00570049004E00310036002D004900520004001E0074006800720065006500620065006500730063006F002E0063006F006D0003003E003000310035003600360073002D00770069006E00310036002D00690072002E0074006800720065006500620065006500730063006F002E0063006F006D0005001E0074006800720065006500620065006500730063006F002E0063006F006D00070008000094D71AE38CD601060004000200000008003000300000000000000000000000003000009050B30CECBEBD73F501D6A2B88286851A6E84DDFAE1211D512A6A5A72594D340A001000000000000000000000000000000000000900220063006900660073002F003100370032002E00310036002E00360036002E0033003600000000000000000000000000

***

## Task 5: Tool Overview 2

Continuing the exploration, the **Files** and **Images** tabs allow us to see exactly what was downloaded or transferred during the capture. The **Parameters** tab is great for identifying HTTP GET or POST variables, while the **Messages** tab reconstructs emails and chats. Finally, the **Anomalies** tab provides a basic IDS-like check for things like spoofing or the EternalBlue exploit.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: What is the name of the Linux distro mentioned in the file associated with frame 63075? (mx-7.pcap)

> **Answer:** CentOS

Question: What is the header of the page associated with frame 75942?

> **Answer:** Password-Ned AB

Question: What is the source address of the image "ads.bmp.2E5F0FD9\[1].bmp"?

> **Answer:** 80.239.178.187

Question: What is the frame number of the possible TLS anomaly?

> **Answer:** 36255

Question: Look at the messages. Which platform sent an email with the subject starting with "You have more"? (mx-9)

> **Answer:** Facebook

Question: What is the email address of Branson Matheson?

> **Answer:** branson@sandsite.org

***

## Task 6: Version Differences

There are two major versions available in the lab VM. Version 2.7 introduced better MAC address processing and can identify MAC address conflicts. However, some older features, like the detailed frame processing and the consolidated cleartext data tab, are only found in version 1.6. Choosing the right version depends on whether we need modern protocol support or granular frame details.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: Which version can detect duplicate MAC addresses?

> **Answer:** 2.7

Question: Which version can handle frames?

> **Answer:** 1.6

Question: Which version can provide more details on packet details?

> **Answer:** 1.6

***

## Task 7: Exercises

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

I applied everything learned to two final cases. By checking the Hosts tab, I could identify the exact OS versions and calculate data transfer amounts between specific IPs. Using the Files and Credentials tabs allowed me to recover passwords and view images transmitted over the network, demonstrating how much information is available in a simple PCAP.

Question: What is the OS name of the host 131.151.37.122? (case1.pcap)

> **Answer:** Windows - Windows NT 4

Question: How many data bytes were received from host 131.151.32.91 to host 131.151.37.122 through port 1065?

> **Answer:** 192

Question: How many data bytes were received from host 131.151.37.122 to host 131.151.32.21 through port 143?

> **Answer:** 20769

Question: What is the sequence number of frame 9?

> **Answer:** 2AD77400

Question: What is the number of the detected "content types"?

> **Answer:** 2

Question: What is the USB product's brand name? (case2.pcap)

> **Answer:** ASIX

Question: What is the name of the phone model?

> **Answer:** Lumia 535

Question: What is the source IP of the fish image?

> **Answer:** 50.22.95.9

Question: What is the password of the "homer.pwned.se@gmx.com"?

> **Answer:** spring2015

Question: What is the DNS Query of frame 62001?

> **Answer:** pop.gmx.com

***

## Conclusion

This room provided a solid foundation for using NetworkMiner in a forensic capacity. I now understand how to quickly map out a network, extract credentials, and reconstruct files from traffic. While Wireshark is still essential for deep analysis, NetworkMiner is an incredible tool for gaining a high-level overview and uncovering artifacts in a fraction of the time.
