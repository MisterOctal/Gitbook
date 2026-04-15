---
icon: fishing-rod
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/62c435d1f4d84a005f5df811/room-content/57a07431ad7464782b0c4cf100368d0c.png
coverY: 0
coverHeight: 139
---

# Phishing Analysis Tools

**Date:** 15.04.2026

**Room Category:** Walkthrough

**Core Concept:** Transitioning from manual analysis to automated toolkits for investigating suspicious emails. This room covers artifact collection, reputation lookups, and the use of interactive malware sandboxes to safely analyze attachments.

This room builds on the foundation from the previous rooms by introducing the tools that enable deeper analysis. By automating the extraction of headers and URLs and utilizing safe execution environments like sandboxes, an analyst can quickly turn a suspicious message into actionable intelligence.

***

## Task 1: Introduction

In the previous rooms of the Phishing Analysis module, we explored the fundamentals of email header and body analysis, along with how to investigate links and attachments by examining the email source code. This room builds on that foundation by introducing the tools that enable deeper analysis. We then apply these skills by investigating three real world phishing cases.

Question: I am ready to learn about phishing analysis tools!

> **Answer:** No answer needed

***

## Task 2: Identifying Artifacts

When analyzing a suspicious or malicious email, the first goal is to collect key artifacts that help determine its legitimacy and intent. These artifacts provide the foundation for deeper investigation such as reputation checks, threat intelligence lookups, and behavioral analysis.

#### Header Artifacts

* **Sender email address:** Where did the email originate from?
* **Sender IP address:** What is the source IP, and what does a reverse lookup reveal?
* **Email subject line:** Does it contain urgency or a call to action?
* **Recipient email address:** Who is the intended recipient (To/CC/BCC)?
* **Reply-To email address:** Where are responses being directed?
* **Date and time:** When was the email sent?

#### Body Analysis Artifacts

<figure><img src="../.gitbook/assets/image (213).png" alt="" width="555"><figcaption></figcaption></figure>

* **URLs and hyperlinks:** Identify all links and expand shortened URLs to reveal their true destination.
* **Attachment name(s):** What files are included, and do their names or extensions appear suspicious?
* **Attachment hash:** Generate a hash value for threat intelligence lookups.

Question: I understand the key indicators to look for when analyzing an email.

> **Answer:** No answer needed

***

## Task 3: Email Header Analysis

Some key information can be viewed directly within an email client, but details like the sender’s IP address and Reply-To information can only be obtained from the email header. Automated tools help streamline this extraction process.

#### Mail Header Analysis Tools

* **Messageheader:** Part of the Google Admin Toolbox. By pasting the full header into the tool, you can extract key details such as the sender’s IP address, routing path, and potential misconfigurations.

<figure><img src="../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

* **Message Header Analyzer:** Performs a similar analysis to parse and visualize header data.

<figure><img src="../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

#### IP and URL Reputation Analysis

When investigating suspected phishing emails, it is important to determine the origin of identified IP addresses and assess the reputations of associated IPs or URLs.

* **IPinfo:** A simple and effective tool for gathering geographic location and associated organization data about an IP address.

<figure><img src="../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

* **URLScan.io:** Enables analysts to safely investigate websites without visiting them directly. It simulates a real user browsing session, records all activity, captures a screenshot, and provides insight into the site's behavior.

<figure><img src="../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

* **Talos IP & Domain Reputation Center:** A threat intelligence tool from Cisco that enables analysts to assess the reputation of IP addresses, domains, and networks to see if they are tied to known malicious activity.

<figure><img src="../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

Question: Use Talos Reputation Center to look up malware-test.com. What is the web reputation assigned to this domain?

> **Answer:** Neutral

***

## Task 4: Email Body Analysis

The email body is where the malicious payload is usually delivered. This can be a hyperlink designed to lure users to a phishing site or an attachment intended to compromise the system.

#### Mail Body Analysis

<figure><img src="../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

Determining the destination of a link can be done by right-clicking the link and selecting "Copy link address" to safely assess the URL. Another effective method is using a URL extraction tool or CyberChef. These tools let you paste raw email content and automatically parse all embedded links to reveal hidden or obfuscated URLs.

<figure><img src="../.gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

#### Email Attachments

Attachments should only be downloaded in a controlled environment like a virtual machine or sandbox. Once safely obtained, you can use tools like the `sha256sum` command in a Linux environment to generate a hash value.

<figure><img src="../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

Once you have the hash, tools like **VirusTotal** allow analysts to check the reputation of files, URLs, IP addresses, and domains using data from dozens of security vendors.

Question: What command can you use in a Linux environment to obtain the SHA256 hash value of an attachment?

> **Answer:** sha256sum

***

## Task 5: Malware Sandboxes

As defenders, we do not need advanced malware analysis skills to fully understand a malicious attachment. We can use online malware sandboxes to safely observe the behavior of a potentially malicious file without risking our own systems.

#### Available Sandboxes

* **ANY.RUN:** An interactive malware sandbox that allows analysts to safely execute and observe suspicious files and URLs in real time. It provides a hands-on experience to interact with the environment, monitor processes, and view network activity.

<figure><img src="../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

* **Hybrid Analysis:** A free malware analysis sandbox that provides detailed insights into file behavior, including system changes, network activity, and indicators of compromise.

<figure><img src="../.gitbook/assets/image (223).png" alt="" width="342"><figcaption></figcaption></figure>

* **JOESandbox:** Created by JOESecurity for advanced malware analysis. It performs both static and dynamic analysis and generates comprehensive reports.

<figure><img src="../.gitbook/assets/image (224).png" alt=""><figcaption></figcaption></figure>

Question: I understand the available sandbox environments for safely analyzing files and URLs.

> **Answer:** No answer needed

***

## Task 6: Using PhishTool

**PhishTool** is a powerful platform that streamlines phishing investigations by combining threat intelligence, OSINT, email metadata, and automated analysis workflows into a centralized view.

<figure><img src="../.gitbook/assets/image (225).png" alt=""><figcaption></figcaption></figure>

Upon uploading an email to PhishTool, it presents the raw HTML, the rendered view, and extracted artifacts. It features tabs for Authentication, Transmission, and URL Analysis.

<figure><img src="../.gitbook/assets/image (226).png" alt=""><figcaption></figcaption></figure>

One standout feature is its integration with VirusTotal, allowing you to view reputation results directly within the workflow.&#x20;

<figure><img src="../.gitbook/assets/image (227).png" alt=""><figcaption></figcaption></figure>

Finally, PhishTool allows you to formally resolve cases by marking the email as malicious and flagging key artifacts.

<figure><img src="../.gitbook/assets/image (228).png" alt="" width="557"><figcaption></figcaption></figure>

Question: According to the VirusTotal analysis from above, which vendor categorized the URLs as phishing?

> **Answer:** SafeToOpen

***

## Task 7: Case Study 1 (Your Account Is on Hold)

In this scenario, we step into the role of a level 1 SOC analyst to identify phishing and spam emails reported by end users. The objective is to review the email `Phish3Case1.eml`, extract key indicators, and identify signs of malicious activity to help the team develop detection rules.

<figure><img src="../.gitbook/assets/image (229).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What reputable brand is this email tailored to impersonate?

> **Answer:** Netflix

Question: Based on the email headers, who is the intended recipient of this message?

> **Answer:** redacted@yahoo.com

Question: In Thunderbird mail use View -> Message Source. What is the Received: from IP address?

> **Answer:** 10.197.37.234

Question: Check out the Return-Path field in the message source. What would you consider a domain of interest based on this field?

> **Answer:** etekno.xyz

Question: Investigate the UPDATE ACCOUNT NOW button. What is the shortened URL?

> **Answer:** https://t.co/yuxfZm8KPg?amp=1

***

## Task 8: Case Study 2 (Update Payment Details)

In this task, we investigate a malicious attachment using ANY.RUN. The email masquerades as a Netflix payment update notification. By reviewing the sandbox results, we can observe the file’s behavior and network activity.

<figure><img src="../.gitbook/assets/image (230).png" alt="" width="563"><figcaption></figcaption></figure>

Question: How does ANYRUN classify this suspected phishing email?

> **Answer:** Suspicious activity

Question: What is the name of the PDF attachment?

> **Answer:** Payment-updateid.pdf

Question: Investigate the email attachment. What is the SHA256 hash of the PDF file?

> **Answer:** cc6f1a04b10bcb168aeec8d870b97bd7c20fc161e8310b5bce1af8ed420e2c24

Question: Check out the ANYRUN text report on the phishing email. Which IP address associated with the process AcroRd32.exe is flagged as malicious?

> **Answer:** 2.16.107.24

Question: Continue investigating the text report. Which Windows process is classed as Potentially Bad Traffic?

> **Answer:** svchost.exe

***

## Task 9: Case Study 3 (Excel Executable)

In this task, we continue our investigation using ANY.RUN, focusing on a different sandbox analysis of a suspected phishing attachment. We review the execution results to identify notable behaviors, process activity, and network connections.

<figure><img src="../.gitbook/assets/image (231).png" alt="" width="563"><figcaption></figcaption></figure>

Question: How does ANYRUN classify the .xlsx attachment?

> **Answer:** Malicious activity

Question: What is the file name of the Excel attachment?

> **Answer:** CBJ200620039539.xlsx

Question: Investigate the Excel attachment. What is the SHA256 hash value?

> **Answer:** 5f94a66e0ce78d17afc2dd27fc17b44b3ffc13ac5f42d3ad6a5dcfb36715f3eb

Question: Check out the ANYRUN text report. What IP address is associated with the malicious domain biz9holdings.com?

> **Answer:** 204.11.56.48

Question: Which other domain is classified as malicious?

> **Answer:** findresults.site

Question: What vulnerability does this malicious attachment attempt to exploit?

> **Answer:** CVE-2017-11882

***

## Conclusion

In this room, we developed a structured approach to phishing analysis by learning what information should be collected during an investigation. We explored a range of tools that support email analysis, performed IP and URL reputation checks, and used sandbox environments to identify indicators of compromise in real world phishing cases.
