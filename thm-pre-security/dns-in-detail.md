---
icon: server
---

# DNS in Detail

Date: 02.03.2026

Room Category: Theory

Core Concept: The Domain Name System (DNS) and how it translates human-readable domains to IP addresses.

As I'm grinding through my HND and prepping for my Security+ this coming November, getting a solid grip on core web protocols is an absolute must. DNS is basically the internet's phonebook. Without it, we'd have to memorize the complex IP addresses of every single website we want to visit. For a future SOC analyst, understanding how DNS works is critical because malware constantly abuses it for command and control (C2) or data exfiltration.

***

## Task 1: What is DNS?

This task just gives a high-level overview. Instead of typing `104.26.10.229` into our browser, DNS lets us just type `tryhackme.com`. It translates the human-friendly name into the machine-friendly IP address so our computer knows exactly where to route the traffic.

Question: What does DNS stand for?

> Answer: Domain Name System

***

## Task 2: Domain Hierarchy

A domain name is actually split into a hierarchy, read from right to left. First, you have the Top-Level Domain (TLD) like `.com` or `.net`. There are also country-code TLDs (ccTLD)—like when I start at University College Birmingham next year, their `ucb.ac.uk` domain uses the `.uk` ccTLD! To the left of the TLD is the Second-Level Domain (the actual name you register), and further to the left are subdomains (like `admin.website.com`).

<figure><img src="../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure>



Question: What is the maximum length of a subdomain?

> Answer: 63

Question: Which of the following characters cannot be used in a subdomain ( 3 b \_ - )?

> Answer: \_

Question: What is the maximum length of a domain name?

> Answer: 253

Question: What type of TLD is .co.uk?

> Answer: ccTLD

***

## Task 3: Record Types

DNS isn't just for IP addresses; it holds various types of records that tell devices how to handle different kinds of traffic. As an analyst, I know I'll be staring at these in log files constantly. The A record maps a name to an IPv4 address, AAAA does the same for IPv6, MX handles email routing, CNAME creates aliases, and TXT is often used for domain verification (or hiding CTF flags!).

Question: What type of record would be used to advise where to send email?

> Answer: MX

Question: What type of record handles IPv6 addresses?

> Answer: AAAA

***

## Task 4: Making A Request

The actual journey of a DNS request is pretty wild. First, your computer checks its local cache. If it doesn't know the IP, it asks a Recursive DNS server (usually your ISP). If _they_ don't know it, they ask the Root servers, which point to the TLD servers, which finally point to the Authoritative server that actually holds the record. It then gets cached locally for a specific amount of time, known as the TTL (Time To Live), so we don't have to repeat the whole process again right away.

<figure><img src="../.gitbook/assets/image (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What field specifies how long a DNS record should be cached for?

> Answer: TTL

Question: What type of DNS Server is usually provided by your ISP?

> Answer: Recursive

Question: What type of server holds all the records for a domain?

> Answer: Authoritative

***

## Task 5: Practical

For this task, we use an in-browser terminal to make actual DNS queries and hunt down specific record types for a dummy website.

<figure><img src="../.gitbook/assets/image (2).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the CNAME of shop.website.thm?

> Answer: shops.myshopify.com

Question: What is the value of the TXT record of website.thm?

> Answer: THM{7012BBA60997F35A9516C2E16D2944FF}

Question: What is the numerical priority value for the MX record?

> Answer: 30

Question: What is the IP address for the A record of www.website.thm?

> Answer: 10.10.10.10

***

## Conclusion

That wraps up DNS in Detail! Knowing the difference between an A record and a CNAME, or a Recursive versus an Authoritative server, is guaranteed to show up on the Security+ exam. More importantly, understanding the normal flow of DNS traffic will make it so much easier to spot anomalous behavior in a SOC environment later on.
