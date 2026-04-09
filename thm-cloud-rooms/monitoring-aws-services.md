---
icon: aws
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767116870785
coverY: 0
coverHeight: 138
---

# Monitoring AWS Services

**Date:** 09.04.2026

**Category:** Technical Analysis

**Core Concept:** Identifying and detecting the most common and impactful attacks targeting AWS services like S3, EC2, and RDS using CloudTrail and Splunk.

While AWS has hundreds of services, the vast majority of cloud breaches stem from a handful of common misconfigurations. This room explores how to hunt for public storage buckets, exposed virtual machines, and emerging threats like Denial of Wallet, giving us the practical workflow to catch these issues early.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (142).png" alt="" width="188"><figcaption></figcaption></figure>

You cannot defend against every possible edge case in a cloud environment. Instead, focusing on the 20% of services that cause 80% of the breaches is the most effective strategy for a SOC team. This lab uses Splunk to dig into CloudTrail logs and GuardDuty alerts to spot these high risk events.

Question: Let's begin!

> **Answer:** No answer needed

***

## Task 2: S3 Attacks and Defenses

Amazon S3 is heavily used for storage, meaning it often holds highly sensitive data like backups or customer records. The main threat here is a bucket accidentally being exposed to the public internet.

#### S3 Exposure Concepts

<figure><img src="../.gitbook/assets/image (143).png" alt="" width="563"><figcaption></figcaption></figure>

AWS has made it harder to accidentally expose buckets. By default, new buckets are private. To actually make one public, an admin has to do two things: disable the "S3 Public Access Block" and then apply a permissive bucket policy (or ACL) that grants `Principal: "*"` access.

As defenders, we want to catch the `PutBucketPolicy` event immediately. If we miss the configuration change, our next best detection opportunity is looking for anomalous `GetObject` or `HeadObject` requests coming from anonymous, external IP addresses.

**Splunk Query used:** `index=task2`

Question: When did Alex disable the "S3 Public Access Block" feature?

> **Answer:** 2025-12-31 17:48:12

_SOC Analyst Note: Found by searching for `eventName=PutBucketPublicAccessBlock` and checking the timestamp._

Question: What is the SID of the applied policy that made the bucket public?

> **Answer:** TempAccessDeniedDebug

Question: Which IP address started the bucket scan soon after it was exposed?

> **Answer:** 212.8.250.220

Question: How many filenames were attempted, and which file was exfiltrated?

> **Answer:** 53, repo.zip

_SOC Analyst Note: Found by filtering for the attacker's IP and counting the number of distinct `GetObject` or `HeadObject` attempts. The successful exfiltration was the one that didn't return an access denied error._

***

## Task 3: EC2 Internet Exposure

EC2 instances are your standard virtual machines. If someone opens up port 22 (SSH) or 3389 (RDP) to the entire internet (0.0.0.0/0), it is only a matter of time before botnets start brute forcing it.

#### Correlating EC2 Logs

The tricky part about monitoring EC2 exposure in CloudTrail is that the logs are fragmented. If someone modifies a Security Group to allow public SSH, the log (`AuthorizeSecurityGroupIngress`) only shows the Security Group ID. It does not tell you which EC2 instance is actually affected. To find the victim machine, you have to pivot and search for `RunInstances` or `ModifyInstanceAttribute` events that reference that specific Security Group ID.

<figure><img src="../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

**Splunk Query used:** `index=task3`

Question: Which security group did Emma create, and which risky service did it expose?

> **Answer:** website-access-sg, SSH

Question: Which EC2 instance ID was created shortly after and uses that security group?

> **Answer:** i-082579354380296e6

_SOC Analyst Note: Found by searching for the Security Group ID (`sg-...`) from the previous event and looking for a `RunInstances` event._

Question: According to the GuardDuty alert, which IP soon attacked the instance?

> **Answer:** 45.78.205.134

Question: When did Emma revoke the insecure rule from the security group?

> **Answer:** 2025-12-31 21:58:34

_SOC Analyst Note: Found by looking for a `RevokeSecurityGroupIngress` event associated with Emma's user._

***

## Task 4: Risks of Public Databases

Database services like RDS or OpenSearch hold the crown jewels. Exposing these to the internet is a catastrophic mistake.

<figure><img src="../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

#### Detecting Database Exposure

Databases rely on a mix of resource policies and network controls (Security Groups/VPCs). Because there are so many ways a database can be accidentally exposed, relying solely on CloudTrail logs can be difficult. This is where Cloud Security Posture Management (CSPM) tools shine. They continuously scan the environment for misconfigurations and can often revert a public database back to private automatically.

<figure><img src="../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

When searching the logs, we look for `ModifyDBInstance` or `CreateDBInstance` events where the "PubliclyAccessible" flag is set to true.

**Splunk Query used:** `index=task4 readOnly=false`

Question: What is the name (instance identifier) of the created RDS instance?

> **Answer:** db-thm-preprod-qa

Question: Which two events indicate the database is Internet-exposed? Provide the first part of their eventID in chronological order.

> **Answer:** dcb54877, 0a3b23c1

_SOC Analyst Note: The first event was `CreateDBInstance` with the public flag set to true, and the second was `AuthorizeSecurityGroupIngress` opening the database port to 0.0.0.0/0._

***

## Task 5: Detecting Cloud Discovery

Before an attacker can steal data or launch instances, they need to figure out where they are and what permissions they have. This phase is known as Discovery.

#### Finding the Signal in the Noise

The AWS Console naturally generates hundreds of discovery like API calls just to render the web pages. To spot an attacker, you need to look for CLI or SDK usage (`aws-cli` user agents) running commands like `GetCallerIdentity` (the AWS equivalent of `whoami`) or `ListAttachedUserPolicies`. Spikes in cross service enumeration from a single programmatic user are a massive red flag.

**Splunk Query used:** `index=task5`

Question: What was the second Discovery command the adversary ran?

> **Answer:** ListAttachedUserPolicies

Question: Which other IAM user did the adversary discover and backdoor?

> **Answer:** lars.andersen

_SOC Analyst Note: Found by looking at the API calls following the discovery phase. The attacker used their initial access to query other users and eventually interacted with Lars._

***

## Task 6: Denial of Wallet Attacks

A Denial of Wallet (DoW) attack targets your billing cycle rather than your uptime.

#### How DoW Works in the Cloud

<figure><img src="../.gitbook/assets/image (147).png" alt="" width="563"><figcaption></figcaption></figure>

Cloud environments are designed to scale. If an attacker hits your web app with a massive DDoS attack, your Auto Scaling groups will just spin up more EC2 instances to handle the load. Your website stays online, but at the end of the month, AWS hands you a bill for thousands of dollars in compute and bandwidth charges. Defending against this requires strict WAF rules, rate limiting, and utilizing CDNs to absorb garbage traffic before it hits your paid compute resources.

Question: What does the acronym DoW stand for?

> **Answer:** Denial of Wallet

Question: Should you monitor DoW with the same effort as DoS? (Yea/Nay)

> **Answer:** Yea

***

## Conclusion

I have to admit, this room took me quite a while to get through. The sheer volume of log data and trying to memorize all the different JSON field names in CloudTrail was pretty overwhelming at first. Tracking an attacker by pivoting from a `requestParameters.groupId` over to a `responseElements.instancesSet.items.instanceId` is a very different muscle than traditional network analysis.

That being said, I am starting to get used to the noise. Once you understand the pattern of how AWS logs actions versus how it logs identity, the massive wall of text starts making a lot more sense. It is a grind, but finding that specific event ID that proves an RDS instance was exposed feels incredibly rewarding.
