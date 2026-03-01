---
icon: chart-network
---

# What is Networking?

Date: 28.02.2026

Room Category: Theory

Core Concept: Basics of computer networks and device communication

Networking is absolutely foundational in cybersecurity. As someone aiming to be a SOC analyst and currently preparing for my Security+ later this year, I know I need to have this stuff locked down! You can't secure or analyze traffic if you don't know how computers actually talk to each other. This room is a great refresher on the basics.

## Task 1: What is Networking?

This task explains the absolute basics. A network is simply a group of things connected together. It doesn't even have to be technology, it could be a postal system or public transport! But in IT, it's technological devices connecting to share resources and communicate with each other.

<figure><img src="../.gitbook/assets/image (45).png" alt="" width="316"><figcaption></figcaption></figure>

Question: What is the key term for devices that are connected together?

> Answer: Network

***

## Task 2: What is the Internet?

The Internet is basically one giant network consisting of millions of smaller networks all hooked together. Fun fact: the first iteration was the ARPANET project in the late 1960s, funded by the US Department of Defense. But the internet as we know it today was created much later.

<figure><img src="../.gitbook/assets/image (46).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Who invented the World Wide Web?

> Answer: Tim Berners-Lee

***

## Task 3: Identifying Devices on a Network

Just like humans use names and fingerprints to identify each other, devices use IP addresses and MAC addresses. IP addresses are logical and can change depending on what network you're on, while MAC addresses are physical, hardcoded to your network interface card at the factory (though they can be spoofed!).

Question: What does the term "IP" stand for?

> Answer: Internet Protocol

Question: What is each section of an IP address called?

> Answer: Octet

Question: How many sections (in digits) does an IP address have?

> Answer: 4

Question: What does the term "MAC" stand for?

> Answer: Media Access Control

In this interactive lab, we get to spoof our MAC address. The scenario is a hotel Wi-Fi network where "Alice" has paid for the Wi-Fi but "Bob" hasn't. We just change Bob's MAC address to match Alice's so the router thinks we are her and lets our packets through.

Question: Deploy the interactive lab using the "View Site" button and spoof your MAC address to access the site. What is the flag?

> Answer: THM{YOU\_GOT\_ON\_TRYHACKME}

***

## Task 4: Ping (ICMP)

Ping is one of the most fundamental network troubleshooting tools out there. It's used to test the reachability of a device on a network and measures the round-trip time for messages. It works by sending ICMP (Internet Control Message Protocol) echo request packets to the target and waiting for an echo reply.

Question: What protocol does ping use?

> Answer: ICMP

Question: What is the syntax to ping 10.10.10.10?

> Answer: ping 10.10.10.10

For the final question, we have to deploy the interactive terminal in the room and ping the Google DNS server (`8.8.8.8`). Doing so gives us a nice little flag to wrap up the room!

<figure><img src="../.gitbook/assets/image (43).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What flag do you get when you ping 8.8.8.8?

> Answer: THM{I\_PINGED\_THE\_SERVER}

***

## Conclusion

This was a relatively simple room, but it's super important. Understanding IPs, MAC addresses, and how basic tools like ping work is critical for anyone wanting to work in cyber, especially when it comes to investigating logs in a SOC.

That's all for this room, see you around!
