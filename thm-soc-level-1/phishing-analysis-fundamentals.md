---
icon: fishing-rod
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/62c435d1f4d84a005f5df811/room-content/57a07431ad7464782b0c4cf100368d0c.png
coverY: 0
coverHeight: 138
---

# Phishing Analysis Fundamentals

**Date:** 15.04.2026

**Room Category:** Walkthrough

**Core Concept:** Analyzing email components including headers, bodies, and attachments to identify malicious intent, spoofing, and social engineering patterns.

Email remains the primary initial access vector for cyber attacks. As a defender, understanding the "under the hood" mechanics of email delivery and header metadata is critical for determining if a message is a legitimate communication or a sophisticated phishing attempt.

***

## Task 1: Introduction

Spam and phishing are the most common social engineering threats facing organizations. Spam is often low risk, but phishing can trick users into disclosing sensitive information or deploying malware.

The role of a defender involves analyzing email components to determine if they are malicious or benign. This investigation helps gather information to harden security measures against future attacks.

Question: I am ready to learn about phishing analysis!

> **Answer:** No answer needed

***

## Task 2: The Email Address

Every phishing email investigation starts with the structure of the email address. The @ symbol is used to separate the user from the destination system.

An email address is composed of three elements:

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

1. Username: The specific recipient mailbox on the server.
2. @ symbol: The separator that tells the system where to route the email.
3. Domain name: Specifies the mail server responsible for receiving the message.

Question: Identify the domain used in the following email address: hatsalesman@tryhatme.com

> **Answer:** tryhatme.com

***

## Task 3: Email Delivery

Several protocols work together to deliver messages from sender to recipient. Each protocol has a specific role in the delivery chain:

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

1. User sends an email: The sender’s email client sends the message to the mail server using SMTP
2. Mail server queries DNS: The sending server asks DNS for the recipient domain’s mail server
3. DNS responds: DNS returns the address of the recipient’s mail server
4. Email is delivered: The message is sent across the Internet to the recipient’s server
5. The recipient checks their mailbox: The recipient’s email client connects to their mail server
6. Email is retrieved: The message is downloaded (POP3) or synced (IMAP) to the recipient’s device



The primary protocols are:

* SMTP: Simple Mail Transfer Protocol. This protocol is used to send emails.
* POP3: Post Office Protocol. This downloads emails to a single device and typically removes them from the server.
* IMAP: Internet Message Access Protocol. This syncs emails across multiple devices and keeps them on the server.

Question: Which protocol is responsible for sending an email from a client to a mail server?

> **Answer:** SMTP

Question: Which service is used to look up the recipient domain’s mail server?

> **Answer:** DNS

Question: Bob wants to access his email from multiple devices, including his phone and laptop. Which protocol should he use?

> **Answer:** IMAP

***

## Task 4: Email Headers

An email consists of two main parts: the header and the body. The header contains metadata about the message and the servers involved in the delivery process.

Key header components found in the email1.eml file:

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* From: The sender email address.
* To: The receiver email address.
* Date: The time the email was sent.
* X-Originating-Ip: The IP address of the originating sender.

Question: What is the full subject line of email1.eml?

> **Answer:** Help protect your budget by protecting your home

Question: View the message source of email1.eml using Thunderbird in your VM. What the IP address listed as the X-Originating-Ip?

> **Answer:** 43.255.56.161

***

## Task 5: Email Body

The body contains the actual message content. This can be plain text or HTML. HTML allows for images, links, and specific styling.

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Attachments are also analyzed in the body section of the raw source code. They are typically stored as Base64 strings. To reconstruct an attachment, an analyst can take the encoded string and use a tool like CyberChef to convert it back to its original file format.

Question: Open up the email2.txt file to view the source of an attachment. What is the Content-Type of the attachment?

> **Answer:** application/pdf

Question: What is the name of the attachment from the previous question?

> **Answer:** zmqpalgh.pdf

Question: Decode the base64 string using either a PDF converter or CyberChef. What is the hidden flag value?

> **Answer:** THM{BENIGN\_PDF\_ATTACHMENT}

***

## Task 6: Types of Phishing

Attackers use various techniques to make emails appear legitimate. Identifying these patterns is key to stopping common phishing traps.

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Common classifications include:

* Spam: Unsolicited bulk emails.
* Phishing: Impersonating a trusted entity to harvest info.
* Spear Phishing: Targeted phishing aimed at a specific individual.
* Whaling: Spear phishing that targets high level executives.

Analysts use "defanging" to make URLs or IP addresses unclickable during an investigation. This prevents accidental infection. For example, `http://` becomes `hxxp[://]`.

Question: Which reputable organization is being spoofed in this phishing attempt?

> **Answer:** Home Depot

Question: What is the sender's email address?

> **Answer:** support@teckbe.com

Question: Inspect the email message source. What is the defanged X-Originating-IP?

> **Answer:** 103\[.]234\[.]236\[.]83

Question: Continue analyzing the email message source. Which mail server generated the Authentication-Results header?

> **Answer:** atlas102.free.mail.gq1.yahoo.com

***

## Task 7: Conclusion

This room covered the anatomy of an email and the path a message takes from sender to recipient. Technical skills were developed to extract and analyze header and body source code.

Business Email Compromise or BEC is a specific attack where an adversary gains access to a legitimate internal account to perform fraudulent actions.

Question: What attack, signified by the acronym BEC, uses a compromised email to trick employees into fraud?

> **Answer:** Business Email Compromise
