---
icon: block-brick-fire
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1723464092304.png
coverY: 0
coverHeight: 138
---

# Firewall Fundamentals

**Date:** 02.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the role of firewalls in network security, including stateful vs. stateless inspection and hands-on configuration of Windows and Linux firewalls.

A firewall acts as a security guard for a network or a single host. By sitting between a trusted internal network and an untrusted external network (like the internet), it monitors and controls incoming and outgoing traffic based on a defined set of security rules. This room covers the foundational principles of firewall operation and provides practical experience in managing built-in security utilities.

***

## Task 1: What Is the Purpose of a Firewall

The primary goal of a firewall is to create a barrier between a private network and the outside world. Much like a security guard at a building entrance, the firewall inspects every packet of data trying to enter or leave.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

If the data packet matches an "Allow" rule, it is permitted to pass. If it matches a "Deny" or "Block" rule, or if it does not match any rule at all (depending on the default policy), it is stopped. This mechanism prevents unauthorized access and protects sensitive internal resources from external threats.

Question: Which security solution inspects the incoming and outgoing traffic of a device or a network?

> **Answer:** Firewall

***

## Task 2: Types of Firewalls

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Firewalls have evolved significantly over the years to keep up with sophisticated attack methods. They are generally categorized by how they inspect data:

* **Stateless Firewalls:** These inspect packets in isolation. They look at the source, destination, and ports but do not remember previous packets. They are fast but less secure because they cannot verify the context of a connection.
* **Stateful Firewalls:** These track the state of active connections. They remember if an incoming packet is a response to an outgoing request, making them much more effective at stopping unauthorized traffic.
* **Proxy Firewalls:** These act as intermediaries, inspecting the contents of all packets at the application layer. They can provide content filtering and anonymity for internal IPs.
* **Next-Generation Firewalls (NGFW):** These provide deep packet inspection (DPI). They can look into the actual data payload of a packet to identify specific applications or malware, regardless of the port being used.

Question: Which type of firewall maintains the state of connections?

> **Answer:** stateful firewall

Question: Which type of firewall offers heuristic analysis for the traffic?

> **Answer:** next-generation firewall

Question: Which type of firewall inspects the traffic coming to an application?

> **Answer:** proxy firewall

***

## Task 3: Firewall Rules and Policies

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Firewalls operate based on a list of rules processed from top to bottom. Once a packet matches a rule, the firewall takes action and stops looking at the rest of the list.

* **Inbound Rules:** Control traffic coming into the network or host.
* **Outbound Rules:** Control traffic leaving the network or host.
* **Default Policy:** The action taken when no rules match. In a high-security environment, the default policy is typically "Deny All," meaning everything is blocked unless specifically allowed.

Question: Which type of action should be defined in a rule to permit any traffic?

> **Answer:** allow

Question: What is the direction of the rule that is created for the traffic leaving our network?

> **Answer:** outbound

***

## Task 4: Windows Defender Firewall

Windows includes a built-in stateful firewall managed through the standard "Windows Defender Firewall" dashboard and its more granular interface, "Windows Defender Firewall with Advanced Security". Because it is deeply integrated into the OS, understanding its nuances is essential for hardening Windows workstations against lateral movement.

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Network Profiles:** Windows determines your current network using Network Location Awareness (NLA) and applies one of three distinct profiles:

* **Domain:** Applied when the system is authenticated to a corporate Active Directory domain controller.
* **Private:** Applied for trusted home or work networks.
* **Public:** Applied in untrusted locations like coffee shops or airports. Here, you might configure the firewall to block all incoming connections for maximum security while allowing essential outbound traffic.

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Advanced Settings and Custom Rules:** Clicking "Advanced Settings" opens a specialized console to create highly customized inbound and outbound rules. Creating a custom rule involves navigating a step-by-step wizard:

1. **Rule Type:** Select between Program, Port, Predefined, or Custom rules.
2. **Protocol and Ports:** Specify the transport protocol (TCP or UDP) and the exact local or remote ports (e.g., ports 80 and 443 for web traffic).
3. **Scope:** Define exactly which local or remote IP addresses the rule applies to.
4. **Action:** Choose to "Allow the connection", "Allow the connection if it is secure", or "Block the connection".
5. **Profile:** Select which network profiles (Domain, Private, Public) this rule should be active on.

During the practical exercise, these custom settings are utilized to explicitly allow or deny incoming traffic for specific operations, such as restricting remote SSH access to a single authorized IP address.

Question: What is the name of the rule that was created to block all incoming traffic on the SSH port?

> **Answer:** Core Op

Question: A rule was created to allow SSH from one single IP address. What is the rule name?

> **Answer:** Infra team

Question: Which IP address is allowed under this rule?

> **Answer:** 192.168.13.7

***

## Task 5: Linux Firewall (iptables, nftables, and UFW)

Linux uses the **Netfilter** framework for packet filtering. Several utilities exist to manage these filters:

* **iptables:** The traditional command-line utility for managing Netfilter rules.
* **nftables:** The modern successor to iptables, offering better performance and a more streamlined syntax.
* **UFW (Uncomplicated Firewall):** A user-friendly wrapper for iptables/nftables commonly found in Ubuntu. It simplifies rule management using natural language commands.

**UFW Examples:**

* `ufw allow 80/tcp` (Allow HTTP)
* `ufw deny 22/tcp` (Block SSH)
* `ufw default deny outgoing` (Block all outgoing traffic by default)

Question: Which Linux firewall utility is considered to be the successor of "iptables"?

> **Answer:** nftables

Question: What rule would you issue with ufw to deny all outgoing traffic from your machine as a default policy? (answer without sudo)

> **Answer:** ufw default deny outgoing

***

## Conclusion

Firewalls remain one of the most critical layers of a defense-in-depth strategy. Whether managing a large enterprise NGFW or simply configuring UFW on a Linux web server, the ability to define clear traffic boundaries is a fundamental security skill. Mastering these built-in utilities ensures that hosts are protected even if perimeter defenses are bypassed.
