---
icon: shield
cover: https://assets.tryhackme.com/room-banners/intro-to-defensive-security.png
coverY: 0
coverHeight: 139
---

# Defensive Security Intro

**Date:** 13.03.2026

**Room Category:** Walkthrough

**Core Concept:** Blue teaming.

I was about to continue down the pre-security path today but I noticed my progress in the Defensive Security Intro room was gone. Upon examination, I found out that they revamped this room so I decided to make a new writeup for the room!

***

## Task 1: Introduction to Defensive Security

This task remained the same, it gives us a brief introduction on the concept of blue teaming.

Question: What is defensive security also known as?

> **Answer:** Blue Teaming

***

## Task 2: Exploring the SOC

This task tells us the key responsibilities of a SOC team. The key duties of a SOC team are: keeping up to date with the latest trends and threats, monitoring for policy violations, establishing acceptable usage policies and normal behaviors, and detecting and responding to breaches.

Question: What would you call a team of cyber security professionals that monitors a network and its systems for malicious events?

> **Answer:** Security Operations Centre

***

## Task 3: Digital Forensics

Digital Forensics is the application of traditional forensics techniques onto the digitla medium. This involves investigating information found on digital media to help solve a crime. The key areas of digital forensics are file systems, system memory, system logs, and, network logs.

Question: An attacker deploys a piece of malicious code that does not save to the disk. What digital forensics technique would we use in this instance?

> **Answer:** System Memory

***

## Task 4: Incident Response

Incident response is how organizations resolve and handle cybersecurity breaches. The process of incident response often looks like this:

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Preparation is the phase where necessary frameworks and resources for incident response are created, detection and analysis is where tools and processes are used to assess the incident's impact. The next phase is where the impact of the incident is reduced and contained, and lastly, post-incident activity is where the incident is reviewed and lessons learned are documented.

Question: What phase of the incident response process involves providing "cyber awareness" training to employees?

> **Answer:** Preparation

***

## Task 5: Practical Example of Defensive Security

In this scenario, a simulated SIEM is used to resolve a possible incident. I'm going to solve this task as if this were a genuine SOC report.

> Report Title: Unauthorized SSH attempt
>
> Date/Time: 13.03.2026 12:36 AM
>
> Summary: Malicious IP address attempted to brute-force SSH and was blocked.

Around 11:23 AM, a SIEM log indicated that an IP address attempted to perform an unauthorized connection to port 22, which is the port used by SSH (Secure Shell). Additional logs indicate that the IP eventually managed to successfully connect to SSH. Likely indicating a SSH brute-force.

Further investigation revealed that the IP address originates from China and has a malicious reputation. The case was then escalated to SOC-Lead, Will Griffin for incident response. Afterwards, permission was granted to block the IP address on the company's firewall so the IP was blocked.

Question: What is the flag that you obtained by following along?

> THREAT-BLOCKED

***

## Conclusion

In conclusion, we learned the basics of defensive security in this room and also experienced a small incident faced by a SOC team. It's funny that this room got revamped... again. But that's all for this room!
