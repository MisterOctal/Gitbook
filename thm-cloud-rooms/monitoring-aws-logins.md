---
icon: aws
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Monitoring AWS Logins

**Date:** 09.04.2026

**Room Category:** Walkthrough

**Core Concept:** Monitoring the AWS control plane to identify common IAM threats, leaked access keys, and role abuse using CloudTrail logs and Splunk.

Securing the cloud starts with the control plane. This room walks through how to track who is logging into your AWS environment, what credentials they are using, and how to spot the difference between legitimate admin activity and an attacker who has stolen a long-lived access key.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

The control plane is the brain of your AWS environment. Authentication is the first line of defense, so we need to know exactly who is getting in and from where. This lab uses Splunk to hunt through CloudTrail logs to identify common attack patterns like brute forcing and credential leaks.

Question: Launch the VM and complete the task!

> **Answer:** No answer needed

***

## Task 2: IAM and User Credentials

IAM (Identity and Access Management) is the core of AWS security. There are three main ways to get into an account:

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **AWS Management Console:** The web-based GUI. This is usually for human users and should always be protected by MFA.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **IAM Access Keys:** These are for programmatic access (CLI/SDK). They are long-lived and dangerous if they leak into public code repos.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **IAM Roles:** These provide temporary security credentials (STS tokens). This is the gold standard for security because the credentials expire quickly and aren't hardcoded.

#### Understanding STS

When you assume a role, AWS returns a set of **STS (Security Token Service)** credentials. These consist of an Access Key, a Secret Key, and a Session Token. Because these expire automatically (usually after 1-12 hours), they are significantly harder for an attacker to maintain persistence with compared to permanent IAM keys.

Question: What type of credential is used to access AWS resources via CLI/SDK?

> **Answer:** Access Key

Question: Which IAM identity type allows you to gain AWS permissions temporarily?

> **Answer:** IAM Role

***

## Task 3: Monitoring Console Logins

Phishing is a major threat for console logins. Attackers set up fake login pages to harvest credentials. We can track these in CloudTrail by looking for the `ConsoleLogin` event.

Example of a ConsoleLogin event:

```json
// Most important fields of the ConsoleLogin event
{
  "eventTime": "2025-12-18T16:48:27Z",
  "eventName": "ConsoleLogin",
  "errorMessage": "Failed authentication",
  "userIdentity": {
    "type": "IAMUser",
    "principalId": "AIDAVZZK4G6EYIICHBXXX",
    "arn": "arn:aws:iam::398985017225:user/john.doe",
    "accountId": "398985017225",
    "userName": "john.doe"
  },
  "sourceIPAddress": "12.42.87.160",
  "userAgent": "Mozilla/5.0 [...]",
  "requestParameters": null,
  "additionalEventData": {
    "LoginTo": "https://console.aws.amazon.com/console/[...]",
    "MobileVersion": "No",
    "MFAUsed": "Yes"
  },
 "recipientAccountId": "398985017225"
}
```

**Splunk Query used:** `index=task3 eventName=ConsoleLogin`

#### Indicators of Compromise (IoC)

When monitoring console logins, analysts look for **MFA Bypass** (where `MFAUsed` is "No") or logins from **anomalous locations**. A successful login from a hosting provider IP (like DigitalOcean or AWS itself) rather than a residential ISP often indicates an attacker is using a VPS to proxy their traffic.

Question: How many times did Thomas fail to log in to the AWS console?

> **Answer:** 11

Question: Which other user logged in to the AWS console without MFA?

> **Answer:** otake.nao

***

## Task 4: Monitoring Access Keys

Leaked access keys are the top cause of cloud breaches. Unlike the console, there is no "login" event for access keys; the credentials are sent with every single API call.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Splunk Queries used:**

* `index=task4 userIdentity.accessKeyId=AKIA*`
* `index=task4 userIdentity.userName="michael.turner"`
* `index=task4 eventSource="s3.amazonaws.com" eventName="DeleteObject"`

#### The Danger of Permanent Keys

Because `AKIA` keys never expire unless manually rotated, an attacker who finds one in a GitHub repo or a `.env` file can stay in an environment indefinitely. Unlike a console session, programmatic access is often "quiet," performing thousands of small actions (like S3 object deletions) that might not trigger standard "login" alerts.

Question: What access key ID of Michael was used in the attack?

> **Answer:** AKIAVZZK4G6EW3NCJENS

Question: What is the name of the S3 bucket accessed by the attackers?

> **Answer:** ocr-passport-scan

_SOC Analyst Note: Found by searching for Michael's username and filtering for S3 service events to see which bucket received the most requests from his IP._

Question: How many files were exfiltrated and deleted by the adversary?

> **Answer:** 26

_SOC Analyst Note: Calculated by searching for `eventName=DeleteObject` and counting the total hits associated with the attacker's session._

Question: Which file was uploaded to the bucket at the end of the attack?

> **Answer:** WHERE-ARE-MY-FILES.README

_SOC Analyst Note: Identified by checking the resource details of the final `PutObject` event in the timeline after the deletions finished._

Question: Which AWS service was used most by the user who did not use access keys?

> **Answer:** Amazon Bedrock

***

## Task 5: Detecting IAM Role Abuse

Roles use temporary credentials starting with `ASIA`. If an attacker uses a custom `roleSessionName`, you have to trace back to the `AssumeRole` event.

**Splunk Queries used:**

* `index=task5 eventName=AssumeRole`
* `index=task5 userIdentity.type=AssumedRole`
* `index=task5 userIdentity.arn="*UserAvatarsProcessor*"`

#### How IAM Role Abuse Works

Attackers often target **Role Chaining**. If an attacker compromises an EC2 instance that has a role attached, they gain full control over it. This was how Capital One Bank got 100 million credit cards exposed. A fairly concerning threat.

Question: Which EC2 instance ID used the UserAvatarsProcessor role?

> **Answer:** i-0d2b8acdedc371589

_SOC Analyst Note: Found by looking at the `userIdentity.arn` which appended the instance ID to the role name._

Question: Someone assumed the EU-RemoteSupport IAM role. How did they name the role session?

> **Answer:** SecretSession

_SOC Analyst Note: Found in the `requestParameters.roleSessionName` field of the AssumeRole event._

Question: Which user assumed the IAM role from the question above?

> **Answer:** sarah.braun

***

## Task 6: Detecting IAM Changes

We monitor for misconfigurations like creating root access keys.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

An example scenario of a root access key credential leak:



#### Detecting Persistence & Backdoors

Attackers who gain initial access via a leaked key will immediately try to create **Persistence**. They do this by calling `CreateAccessKey` for a high-privileged user (or even the Root user). This ensures that if the SOC rotates the original leaked key, the attacker still has a "backdoor" key to get back in.

#### The Criticality of Root Monitoring

The **Root User** should never have active Access Keys for daily operations. If you see a `CreateAccessKey` event where the ARN involves `root`, it is an immediate high-severity incident. It indicates that either an administrator is following exceptionally poor security practices or, more likely, an attacker is attempting to cement full control over the account.

**Splunk Query used:**

* `index=task6 eventName=CreateAccessKey`
* `index=task6 userIdentity.arn="*root*"`

Question: Under which ARN does the Splunk integration authenticate?

> **Answer:** arn:aws:iam::398985017225:root

Question: When was the over-privileged integration access key created?

> **Answer:** 2025-12-29 19:59:23

_SOC Analyst Note: Located by searching for the specific Access Key ID and looking at the `eventTime` of its creation event._

***

## Conclusion

Let’s be real this room was a heavy lift. Monitoring AWS is a massive exercise in patience because of the **sheer volume of field names**. Between mapping `userIdentity.arn` to `requestParameters.roleSessionName` and tracking `AKIA` vs `ASIA` keys, it’s easy to get overwhelmed.

Cloud security analysis isn't just about knowing the "bad guys"; it's about mastering the "map." Getting through this lab means you've successfully navigated a maze of JSON structures and complex Splunk queries. The fatigue is part of the process, connecting these dots is what separates a good analyst from a great one.
