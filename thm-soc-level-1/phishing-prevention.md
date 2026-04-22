---
icon: fishing-rod
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/62c435d1f4d84a005f5df811/room-content/57a07431ad7464782b0c4cf100368d0c.png
coverY: 0
coverHeight: 138
---

# Phishing Prevention

**Date:** 17.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Defending against phishing emails using authentication protocols (SPF, DKIM, DMARC, S/MIME), analyzing SMTP traffic, and implementing modern security controls.

Phishing remains one of the most prevalent initial access vectors for cyber attacks. Securing email infrastructure is a fundamental requirement for any organization. We will also analyze real SMTP traffic captures to understand how mail servers process messages and block malicious activity.

***

## Task 1: Introduction

This room covers the core protocols used to protect email domains from spoofing and impersonation. A target machine (PhishingEmails4 v1.1-badr) is provided to analyze real world packet captures containing SMTP traffic. Deploy the machine to follow along with the practical analysis tasks later in the room.

Question: I understand the learning objectives and am ready to learn about phishing prevention!

> **Answer:** No answer needed

***

## Task 2: Sender Policy Framework (SPF)

Sender Policy Framework (SPF) is used to authenticate the sender of an email. With an SPF record in place, Internet Service Providers can verify that a mail server is authorized to send email for a specific domain. An SPF record is a DNS TXT record containing a list of the IP addresses that are allowed to send email on behalf of that domain.

**The SPF Workflow:** When an email is sent, the receiving mail server checks the domain's SPF record to verify whether the sending server is authorized. The delivery of the email is based on the result of the SPF record verification:

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Pass, Neutral, None:** Accept (Allow and process the email)
* **SoftFail, PermError:** Flag (Mark as suspicious but allow)
* **Fail, TempError:** Reject (Immediately discard the email)

**SPF Record Syntax:** A sample SPF record looks like this: `v=spf1 ip4:127.0.0.1 include:_spf.google.com -all`

* `v=spf1`: Signifies the start of the SPF record.
* `ip4:127.0.0.1`: Specifies which IP can send mail.
* `include:_spf.google.com`: Specifies which external domain can send mail on behalf of this domain.
* `-all`: Non-authorized emails will be rejected.

**Tools for SPF Analysis:** Tools like dmarcian's SPF Surveyor enable visual inspection of DNS records to ensure correct syntax. For example, a domain might not list explicit IP addresses but instead use `include` tags to authorize third party services (like Google, Chargebee, or Hubspot).&#x20;

<figure><img src="../.gitbook/assets/image (12) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Additionally, tools like Google Admin Toolbox Messageheader allow analysts to view delivery details using an email's full header. If an IP address is unknown to the SPF record, it may return a SoftFail, meaning the receiving server will accept the email but flag it as suspicious.

<figure><img src="../.gitbook/assets/image (13) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Based on TryHackMe's SPF record above, how many domains are authorized to send email on its behalf?

> **Answer:** 3

Question: What is the intended action of an email that returns a SoftFail verification result?

> **Answer:** Flag

***

## Task 3: DomainKeys Identified Mail (DKIM)

DomainKeys Identified Mail (DKIM) is used for the authentication of an email being sent. Like SPF, DKIM is an open standard for email authentication used for DMARC alignment. A DKIM record exists in the DNS but is more complex than SPF. DKIM's advantage is that it can survive email forwarding, making it a superior foundation for securing email.

<figure><img src="../.gitbook/assets/image (14) (1).png" alt="" width="563"><figcaption></figcaption></figure>

**The DKIM Workflow:** When an email is sent, the sending mail server uses a private key to add a digital signature to the email. The receiving server retrieves the public key from the domain's DNS DKIM record to verify that the message truly came from the domain. If the signature matches, the email is authentic; otherwise, it may be flagged or rejected.

**DKIM Record Syntax:** A sample DKIM record looks like this: `v=DKIM1; k=rsa; p=<public_key>`

* `v=DKIM1`: Specifies the version of DKIM being used (optional).
* `k=rsa`: The key type. The RSA encryption algorithm is standard.
* `p=`: The public key that will be matched to the private key to verify the DKIM signature.

If an email header shows a DKIM result of `permerror`, this indicates a permanent failure in DKIM verification. This could result from an invalid signature, a missing DNS record, forwarding modifications, or a misconfiguration.

<figure><img src="../.gitbook/assets/image (15) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Based on the sample header above, what is the reason for the permerror?

> **Answer:** no key for signature

***

## Task 4: Domain-Based Message Authentication, Reporting, and Conformance (DMARC)

Domain-Based Message Authentication, Reporting, and Conformance (DMARC) uses a concept called alignment to tie the result of SPF and DKIM to the content of an email. DMARC ensures the sender's domain matches the domains verified by SPF and DKIM. If the alignment fails, DMARC instructs the recipient server on how to handle the email based on a policy specified in the record.

<figure><img src="../.gitbook/assets/image (16) (1).png" alt="" width="563"><figcaption></figcaption></figure>

**DMARC Record Syntax:** A sample DMARC record looks like this: `v=DMARC1; p=quarantine; rua=mailto:postmaster@website.com`

* `v=DMARC1`: The version of DMARC (required).
* `p=quarantine`: The DMARC policy (quarantine tells the receiver to move failures to the spam folder).
* `rua=mailto:postmaster@website.com`: An optional tag specifying where aggregate reports will be sent.

Other policies include `p=none` (monitor only) and `p=reject` (block the email entirely). Tools like the dmarcian Domain Checker inspect DMARC, SPF, and DKIM records collectively to identify any alignment or syntax issues.

Question: Which DMARC policy provides the greatest amount of protection by blocking emails that fail the DMARC check?

> **Answer:** p=reject

***

## Task 5: Secure/Multipurpose Internet Mail Extensions (S/MIME)

Secure/Multipurpose Internet Mail Extensions (S/MIME) is a standard protocol for sending digitally signed and encrypted messages. It is based on public key cryptography, where the private key is never shared and the public key can be distributed openly.

**Core S/MIME Components:**

<figure><img src="../.gitbook/assets/image (17) (1).png" alt="" width="563"><figcaption></figcaption></figure>

1. **Digital Signature:** The sender signs the message with their private key, and the recipient verifies the sender's identity using the sender's public key. This provides:

* **Authentication:** Confirms the sender's identity through their digital certificate.
* **Non-repudiation:** Ensures the sender cannot deny sending the message.
* **Data Integrity:** Detects any changes to the message after it is signed.

2. **Encryption:** The sender encrypts the message using the recipient's public key, allowing only the recipient to decrypt it with their private key. This provides:

* **Confidentiality:** Keeps the content private and readable only by the intended recipient.

Question: Which S/MIME component ensures that only the intended recipient can read the contents of an email message?

> **Answer:** Encryption

***

## Task 6: Analyzing SMTP Responses

This task involves analyzing a PCAP file containing SMTP traffic using Wireshark. Understanding SMTP status codes is crucial for determining how mail servers interact and why certain messages are rejected or delivered.

When reviewing packet captures, filtering for SMTP responses allows analysts to quickly identify successful deliveries or blocked spam attempts. Specific response codes (like the 500 series) indicate permanent errors, such as a mailbox not existing or a message being blocked by a spam filter.

Question: Which Wireshark filter can you use to narrow down your results based on SMTP response codes?

> **Answer:** smtp.response.code

Question: How many packets in the capture contain the SMTP response code 220 Service ready?

> **Answer:** 19

Question: One SMTP response indicates that an email was blocked by spamhaus.org. What response code did the server return?

> **Answer:** 553

Question: Based on the packet from the previous question, what is the full Response code: message?

> **Answer:** Requested action not taken: mailbox name not allowed (553)

Question: Search for response code 552. How many messages were blocked for presenting potential security issues?

> **Answer:** 6

***

## Task 7: Inspecting Emails and Attachments

Moving beyond basic SMTP status codes, analysts use the Internet Message Format (IMF) to examine the inner details of emails. IMF allows us to view sender and recipient fields, content types, and potentially malicious attachments directly within the packet capture.

Filtering by `imf` in Wireshark exposes the raw email structure, revealing which email clients were used to construct the message and how attachments are encoded for transit over the network.

Question: How many SMTP packets are available for analysis?

> **Answer:** 512

Question: What is the name of the attachment in packet 270?

> **Answer:** document.zip

Question: According to the message in packet 270, which Host IP address is not responding, making the message undeliverable?

> **Answer:** 212.253.25.152

Question: By filtering for imf, which email client was used to send the message containing the attachment attachment.scr?

> **Answer:** Microsoft Outlook Express 6.00.2600.0000

Question: Which type of encoding is used for this potentially malicious attachment?

> **Answer:** base64

***

## Task 8: How Organizations Stop Phishing

Modern email systems employ a combination of technical controls and user education to mitigate phishing risks.

**Technical Defenses:**

* **Email Filtering:** Provides filtering based on IP and domain reputation, blocking or quarantining suspicious messages.
* **Secure Email Gateways (SEGs):** Scan messages to detect impersonation attempts, spoofing, and advanced phishing techniques.
* **Link Rewriting:** Replaces suspicious or unknown URLs with safe, redirected ones, giving the system time to scan and verify the link upon clicking.
* **Sandboxing:** Isolates and tests suspicious links or attachments in a secure, virtual environment to observe malicious behavior safely.

**User-Facing Tools & Training:**

<figure><img src="../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

* **Trust & Warning Indicators:** Visual cues (like banners stating "External Sender" or "Suspicious Link") help users gauge message safety.
* **Phishing Reporting:** Easy, in-email buttons that let users quickly report suspicious messages to the security team.
* **User Awareness Training:** Educating employees on identifying phishing attempts and social engineering tactics.
* **Phishing Simulation Exercises:** Controlled phishing campaigns used to test and reinforce employee training.

Question: A security team wants to implement a control to detect hidden malware inside email attachments. They need a way to analyze suspicious files without risking infection on real systems. Which protective technique would allow them to observe a file's behavior safely?

> **Answer:** Sandboxing

***

## Conclusion

Throughout this room, we explored how technologies like SPF, DKIM, DMARC, and S/MIME work together to authenticate email senders, ensure message integrity, and protect users against phishing. We applied this knowledge by interpreting SMTP response codes and analyzing SMTP traffic to understand how email servers handle messages in the real world.
