---
icon: chart-network
---

# Extending Your Network

Date: 01.03.2026

Room Category: Theory

Core Concept: Firewalls, VPNs, and advanced networking devices

We're moving beyond the basics of a simple LAN and looking at how networks are extended and protected. Knowing how firewalls filter traffic, how VPNs secure data across public networks, and the difference between routing and switching is huge for my Security+ exam. From a SOC perspective, firewalls and routers are going to be my primary sources for logs and alerts, so understanding their core functions is essential!

***

## Task 1: Introduction to Port Forwarding

Building right off our previous knowledge of ports, port forwarding is how we allow external devices on the internet to access a specific service on our internal LAN. Since our LAN devices usually have private IP addresses that can't be reached from the outside, the router acts as the middleman. We tell the router, "Hey, if traffic comes in on port 80, forward it to this specific internal server." It's incredibly useful but can be a huge security risk if misconfigured!

<figure><img src="../.gitbook/assets/image (20).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the name of the device that is used to configure port forwarding?

> Answer: Router

***

## Task 2: Firewalls 101

Firewalls are the ultimate gatekeepers of a network, making them one of the most important tools for a SOC Analyst. This task breaks down the difference between stateless and stateful firewalls. Stateless firewalls are basic—they just look at individual packets and check them against a set of rules (like an ACL). Stateful firewalls are much smarter; they track the entire active connection and understand the context of the traffic, which makes them much better at stopping modern attacks.

<figure><img src="../.gitbook/assets/image (21).png" alt="" width="188"><figcaption></figcaption></figure>

Question: What layers of the OSI model do firewalls operate at?

> Answer: 3 & 4

Question: What category of firewall inspects the entire connection?

> Answer: Stateful

Question: What category of firewall inspects individual packets?

> Answer: Stateless

***

## Task 3: Practical - Firewall

In this question, we have to play a minigame where we have to block a specific IP from accessing the webserver through port 80. To do this we simply configure the firewall rule to DROP all packets from 198.51.100.34 to the web server - 203.0.110.1 on port 80. After this, we're rewarded with a flag.

Question: What is the flag?

> Answer: THM{FIREWALLS\_RULE}

***

## Task 4: VPN Basics

VPNs (Virtual Private Networks) are what allow employees to work remotely while still securely accessing the corporate network. They create an encrypted "tunnel" over the public internet. As an analyst, I'll need to know the difference between site-to-site VPNs (connecting two entire offices) and remote access VPNs (connecting a single user's laptop to the office). We also touch on the underlying technologies like IPSec, which handles the heavy lifting for encryption and authentication.

<figure><img src="../.gitbook/assets/image (22).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What VPN technology only encrypts & provides the authentication of data?

> Answer: PPP

Question: What VPN technology uses the IP framework?

> Answer: IPSec

***

## Task 5: LAN Networking Devices

This task goes deeper into the hardware that makes a network function. Routers are the devices that connect different networks together (operating at Layer 3 of the OSI model) by "routing" traffic. Switches connect devices within the _same_ network (operating at Layer 2). However, as networks get more advanced, we now have Layer 3 Switches, which blur the line by doing the job of a switch but with the routing capabilities of a router!

<figure><img src="../.gitbook/assets/image (24).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the verb for the action that a router does?

> Answer: Routing

Question: What are the two different layers of switches? Separate these by a comma I.e.: Layer X,Layer Y

> Answer: Layer 2, Layer 3

***

## Task 6: Practical - Network Simulator

In the last question, we have to deploy a static lab and make a message from computer 1 get to computer 3. Upon successfully getting the message to computer 3, a detailed animation of how the packets flow is dispalyed and we receive a flag as well. Additionally, the network log can be monitored to answer the second question.

Question: What is the flag from the network simulator?

> Answer: THM{YOU'VE\_GOT\_DATA}

Question: How many HANDSHAKE entries are there in the Network Log?

> Answer: 5

***

## Conclusion

That covers "Extending Your Network"! This room was great for bridging the gap between basic LAN theory and actual enterprise network security. Understanding firewalls, port forwarding, and VPNs are fundamental concepts I'll need when triaging alerts or mapping out an attack path. One step closer to being ready for the Security+ exam!
