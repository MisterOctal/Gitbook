---
icon: fire-extinguisher
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1718961567632
coverY: 0
coverHeight: 138
---

# IR Fundamentals

**Date:** 01.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the incident response lifecycle, severity levels, and common frameworks like SANS and NIST.

Incident Response is the backbone of defensive security. It is not just about reacting to a hack; it is about having a structured plan to identify, contain, and eliminate threats before they cause irreversible damage. In this room, the focus is on how organizations move from a simple "alert" to a full-scale response, ensuring that the impact on business operations is kept to an absolute minimum.

***

## Task 1: Introduction to Incident Response

Imagine living in a heavily insecure street with many expensive things in your home. Security guards and CCTV are great proactive measures, but have you considered what happens if someone successfully bypasses those mechanisms? This is the core of defensive planning. In the digital realm, these occurrences are referred to as Cyber Security Incidents.

<figure><img src="../.gitbook/assets/image (106).png" alt="" width="299"><figcaption></figcaption></figure>

Just as one would plan for physical security, cyber security incidents require planning and resources to avoid massive losses. Incident Response handles an incident from start to finish, providing a thorough guideline for preventing incidents, fighting them when they occur, and effectively minimizing their impact.

Question: Click me to proceed to the next task.

> **Answer:** No answer needed

***

## Task 2: What are Incidents?

Processes running on a device generate massive numbers of events, which security solutions ingest as logs. When a solution finds events associated with potentially harmful activity, it triggers an alert.

Analyzing these alerts is necessary to distinguish between False Positives (benign events) and True Positives (actual threats). For example, a high amount of data transfer might be a cloud backup or a malicious exfiltration. Once an alert is categorized as a true positive, it becomes an incident. These are assigned a severity level: low, medium, high, or critical to help prioritize which threats need a response first.

Question: What is triggered after an event or group of events point at a harmful activity?

> **Answer:** Alert

Question: If a security solution correctly identifies a harmful activity from a set of events, what type of alert is it?

> **Answer:** true positive

Question: If a fire alarm is triggered by smoke after cooking, is it a true positive or a false positive?

> **Answer:** false positive

***

## Task 3: Types of Incidents

Categorizing incidents helps in applying the most effective response strategy. Labeling everything as a generic hacking attempt is not specific enough for security professionals.

<figure><img src="../.gitbook/assets/image (97).png" alt="" width="188"><figcaption></figcaption></figure>

* **Malware Infections:** Malicious programs like viruses or ransomware that can damage a system. Most incidents involve these infections.

<figure><img src="../.gitbook/assets/image (98).png" alt="" width="188"><figcaption></figcaption></figure>

* **Security Breaches:** Unauthorized access to confidential data that should remain private.

<figure><img src="../.gitbook/assets/image (99).png" alt="" width="188"><figcaption></figcaption></figure>

* **Data Leaks:** The exposure of sensitive info, sometimes caused unintentionally by human error or misconfigurations.

<figure><img src="../.gitbook/assets/image (100).png" alt="" width="188"><figcaption></figcaption></figure>

* **Insider Attacks:** Incidents initiated by someone within the organization, such as a disgruntled employee. These are dangerous because the attacker already has legitimate access.

<figure><img src="../.gitbook/assets/image (101).png" alt="" width="188"><figcaption></figcaption></figure>

* **DoS Attacks:** An attacker floods a system with false requests to exhaust resources and make services unavailable.

Question: A user’s system got compromised after downloading a file attachment from an email. What type of incident is this?

> **Answer:** Malware Infection

Question: What type of incident aims to disrupt the availability of an application?

> **Answer:** Denial of Service

***

## Task 4: Incident Response Process

Handling incidents requires a structured, repeatable process. SOC teams rely on two major frameworks: SANS and NIST. Both organizations have developed these standards to ensure effective responses.

<figure><img src="../.gitbook/assets/image (102).png" alt="" width="375"><figcaption></figcaption></figure>

The SANS framework consists of six phases, known by the acronym **PICERL**: Preparation, Identification, Containment, Eradication, Recovery, and Lessons Learned.&#x20;

<figure><img src="../.gitbook/assets/image (103).png" alt="" width="375"><figcaption></figcaption></figure>

NIST follows a similar path but condenses the process into four main phases. These methodologies are documented in an **Incident Response Plan**, a formal document that defines roles, responsibilities, and communication plans for active threats.

Question: The Security team disables a machine’s internet connection after an incident. Which phase of the SANS IR lifecycle is followed here?

> **Answer:** containment

Question: Which phase of NIST corresponds with the lessons learned phase of the SANS IR lifecycle?

> **Answer:** Post-Incident Activity

***

## Task 5: Incident Response Techniques

<figure><img src="../.gitbook/assets/image (104).png" alt="" width="375"><figcaption></figcaption></figure>

Manual hunting for abnormal behavior is impossible in large environments, so specialized security solutions are utilized. **SIEM** tools correlate logs in a central location, **AV** (Antivirus) scans for known malware, and **EDR** (Endpoint Detection and Response) provides advanced protection and the ability to contain threats directly on the host.

<figure><img src="../.gitbook/assets/image (105).png" alt="" width="188"><figcaption></figcaption></figure>

To keep responses consistent, **Playbooks** are used. These comprehensive guidelines provide step-by-step instructions for specific incident types, such as a phishing email. They outline exactly who to notify, how to analyze the email, and when to isolate the system, ensuring an effective methodology is followed even under pressure.

Question: Step-by-step comprehensive guidelines for incident response are known as?

> **Answer:** Playbooks

***

## Task 6: Lab Work Incident Response

In this practical scenario, an investigation is conducted into a phishing incident where an employee downloaded a malicious attachment named "Payslip.pdf." The goal is to track how far the infection has spread, as phishing campaigns often target multiple employees.

The provided toolset allows for an analysis of the network to see which hosts merely downloaded the file and which ones executed it. The process involves moving uninfected devices to quarantine as a precaution and performing a deep dive into the infected host to analyze the timeline of events.

Question: What was the name of the malicious email sender?

> **Answer:** Jeff Johnson

Question: What was the threat vector? (Question Hint Email Attachment/URL)

> **Answer:** Email Attachment

Question: How many devices downloaded the email attachment?

> **Answer:** 3

Question: How many devices executed the file?

> **Answer:** 1

Question: What is the flag found at the end of the exercise?

> **Answer:** THM{My\_First\_Incident\_Response}

***

## Conclusion

This room provided a solid walkthrough of the fundamental building blocks of Incident Response. From understanding the difference between a simple event and a full-scale incident to practicing containment in a lab environment, these concepts are vital for any security professional. Mastering the transition between identification and containment is a key skill for future work in a SOC.
