---
icon: shield
---

# Defensive Security Intro

Date: 26.02.2026

Room Category: Theory

Core Concept: Blue teaming

Every month or so when we check the headlines, we can see cases where defensive security fails. Whether is be large-scale data breaches involving millions of records, or ransomware attacks compromising entire networks, the impact of poor defensive security can greatly affect an organization.

***

## Task 1: Introduction to Defensive Security

Question: What is defensive security also known as?

> Answer: Blue Teaming

***

## Task 2: Responsibilities of Defensive Security

Some of the key areas of defensive security are: monitoring and detecting, incident response, threat intelligence, vulnerability management, and investigation and analysis. It is also important to note that defensive security teams are made up of a mixture of roles and responsibilities, such as: SOC analysts, incident responders, security engineers, and digital forensics experts.

In the example provided by THM, we have four team members:

* Bob, a SOC Analyst
* Aaliyah, an Incident Responder
* Zoe, a Security Engineer
* Bill, a Digital Forensics Expert

Question: An attack has been detected on an organisation's network. What is the name of the person above who would be responsible for responding to the attack?

> Answer: Aaliyah

***

## Task 3: Defensive Security in Practice

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

In practice, organizations never rely on a single tool or method to remain secure. They utilize layers of defensive, using various tools and policies to ensure security. This is called 'Defense in Depth', meaning that even if one security measure fails, various others exist to ensure security.

Some common defensive SOC tools and policies are: intrusion detection systems (IDS), firewalls, employee training, and security policies.

A Security Operation Center (SOC) is a defensive security center for an organization's technology. This is the frontline against the threats to an organization and operate 24/7. A typical day in SOC involves reviewing alerts triggered by the security tools, investigating anomalies, and responding to incidents.

A SIEM (Security Information and Event Management) system is a platform that aggregates various data and information from security devices, work stations, servers and more.

Question: What is the abbreviation for the term "Security Operations Centre"?

> Answer: SOC

***

## Task 4: Practical: Defend Fakebank

In this question, we will join FakeBank's defensive security team and resolve a security event using their SIEM. The attack we have to investigate seems to be a similar attack to the one we conducted previously in the Offensive Security Intro room. It's a web discovery attack, meaning the attacker attempted to enumerate and discover the pages on FakeBank.

<figure><img src="../.gitbook/assets/image (5) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Upon clicking 'Investigate' we discover that the attack was conducted by an IP address - 32.122.195.63. Further discovery indicates they attempted to locate the administrator page, although the attack found the administrator page, the attacker received a 403 status code, meaning they weren't able to access it.

<figure><img src="../.gitbook/assets/image (6) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Lastly, we also found out that the IP has a malicious reputation and originates from Moscow. Yikes.

After concluding our investigation, I blocked the IP address for 72 hours, set a rate limit for admin endpoints, and updated our WAF rules to block suspicious enumeration attempts. After tuning our security tools, we received a flag - THM{FAKEBANK-SECURED}.

Question: What is the flag that you obtained by following along?

> Answer: THM{FAKEBANK-SECURED}

***

## Conclusion

In conclusion, we learned the basics of defensive security in this room and tuned our security tools to handle directory enumeration attacks better. This room was fairly simple but effective. That's all for this room, goodbye!
