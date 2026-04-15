---
icon: fishing-rod
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/62c435d1f4d84a005f5df811/room-content/57a07431ad7464782b0c4cf100368d0c.png
coverY: 0
coverHeight: 138
---

# Phishing Emails in Action

**Date:** 15.04.2026

**Room Category:** Walkthrough

**Core Concept:** Transitioning from theory to practice by analyzing real-world phishing samples. This room focuses on social engineering tactics, link manipulation, tracking pixels, and credential harvesting.

While the fundamentals cover "how" an email is built, this session explores "how" attackers use those components to mirror legitimate services like PayPal, DHL, and Microsoft to trick users into performing malicious actions.

***

## Task 1: Introduction

The objective of this room is to identify subtle nuances that distinguish routine notifications from sophisticated attacks. This includes detecting link manipulation, analyzing tracking pixels, and deconstructing multi-stage redirection chains. By studying these samples, an analyst learns to look past the surface level branding to find the technical inconsistencies that betray an attacker's presence.

***

## Task 2: Case Study: Cancel Your Order

This sample mimics an official transaction receipt to create a sense of urgency. The goal is to panic the user into believing an unauthorized purchase has been made, which prompts them to click a "Cancel" button without verifying the source.

#### Techniques Used

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

* **Spoofed Email Address:** The display name shows `service@paypal.com` but the actual address is `gibberish@sultanbogor.com`. This is an immediate red flag found in the headers.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

* **URL Shortening:** The "Cancel the order" button uses a shortening service to hide the final malicious landing page. Shortened URLs are common in phishing because they prevent the user from seeing the actual destination domain when hovering over the link.
* **Tools:** Services like **WhereGoes** can be used to follow the redirection chain of a shortened URL safely without visiting the malicious site directly.

Question: Who is listed as the Merchant in the email body?

> **Answer:** Amazing Stuff

***

## Task 3: Case Study: Track Your Package

Attackers frequently use fake tracking numbers to prompt users to click. This exploit leverages the high volume of modern delivery notifications to blend in with legitimate shipping alerts from companies like UPS or FedEx.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

#### Pixel Tracking

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

In this sample, an invisible image (`Tracking.png`) is embedded. These **tracking pixels** notify the sender when the email is opened, confirming that the recipient's email address is active and the user is susceptible to interaction. This allows the attacker to validate their target list for future, more targeted campaigns. Many email providers now block images by default to prevent this specific type of telemetry.

Question: What root domain does the hyperlink in the above example point to? (Defanged)

> **Answer:** devret\[.]xyz

***

## Task 4: Multi-Stage Redirection

This campaign uses a chain of trusted brands (OneDrive to Adobe to Microsoft) to build a false sense of security before reaching a **Credential Harvesting** portal. By using recognizable logos and familiar workflows, the attacker lowers the victim's guard as they move through the different "document sharing" steps.

#### The Redirection Chain

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

1. User clicks "Download Document" in an email that claims to have an expiration date for added urgency.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

2. User lands on a fake OneDrive page that acts as a bridge.
3. Clicking "Get Document" leads to a fake Adobe page with nonsensical instructions.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

4. Finally, the user is presented with a fake Outlook login portal designed to steal usernames and passwords.

Question: The attacker deployed a fake portal to capture and exfiltrate user credentials. What is this type of attack called?

> **Answer:** Credential Harvesting

***

## Task 5: Case Study: Your Account Is on Hold

This variant introduces an attachment rather than a direct link to bypass standard email filters that might flag suspicious URLs in the body text. This moves the "malicious" part of the interaction into a file that a user might trust more than a link.

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

#### Red Flags

* **Typos:** Noticeable misspellings of the brand name (e.g., "Netllx"). This is a common tactic to bypass keyword-based spam filters.
* **Atypical Phone Numbers:** Phone formats that do not match the expected region or company standards often appear in the footer of these fake emails.

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

* **PDF Attachments:** The link to "Update Payment Account" is hidden inside the PDF. Because PDFs can be formatted to look exactly like official documents, they are excellent for hiding malicious redirects.

Question: What is the actual sender email address hidden behind the Netllx billing display name?

> **Answer:** z99@musacombi.online

***

## Task 6: Case Study: Your Recent Purchase

This attempt relies on a completely blank email body and a suspicious attachment format. It targets the curiosity of the user: when they see a blank email with an attachment titled "Receipt," they are tempted to open it to see what they supposedly bought.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

#### BCC and Unusual Extensions

* **BCC (Blind Carbon Copy):** The victim is not the direct recipient. This suggests a bulk automated campaign where the attacker is blasting thousands of addresses at once.

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

* **.dot Files:** The attachment is a Microsoft Word Template. This is an unusual format for a receipt and is often used to host malicious macros or embedded objects that trigger a browser redirect when clicked.

Question: What does the acronym BCC stand for?

> **Answer:** Blind Carbon Copy

Question: What is the file extension of the attachment?

> **Answer:** .dot

***

## Task 7: Case Study: Scheduled Shipment

This sample funnels the victim toward a malicious Excel attachment designed to execute a payload on the local machine. Unlike credential harvesters, the goal here is direct system compromise.

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

#### Conflicting Markers

The document shows significant geographical inconsistencies that reveal it as a fake:

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

* **Sender:** German domain.
* **Address:** City in India.
* **Content:** Written in Mandarin.

#### The Payload

The link within the Excel document attempts to download and run an executable named `regasms.exe`. If successful, this can lead to persistence (maintaining access after a reboot), data exfiltration, or the deployment of ransomware to encrypt the victim's files.

Question: What is the name of the executable that the Excel attachment attempts to run?

> **Answer:** regasms.exe

***

## Conclusion

Phishing analysis is an iterative process. By combining header analysis with body and attachment forensics, an analyst can build a complete picture of the adversary's intent. The most effective defense is a skeptical approach to every "urgent" notification, regardless of how professional the branding appears. This room demonstrates that attackers will use any combination of links, tracking pixels, and file attachments to achieve their goals.
