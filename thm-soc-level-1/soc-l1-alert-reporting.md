---
icon: triangle-exclamation
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1743519608802
coverY: 0
coverHeight: 139
---

# SOC L1 Alert Reporting

**Date:** 07.04.2026

**Room Category:** Walkthrough

**Core Concept:** Learning the critical post-triage steps: reporting findings, escalating high-risk threats to L2 analysts, and communicating with other departments.

Once an alert is triaged, the L1 analyst must document their findings. This documentation ensures that the context is preserved for long-term records and provides a roadmap for L2 analysts if the alert is escalated for deeper investigation.

***

## Task 1: Introduction

This room builds upon the triage skills learned previously, focusing on how to effectively communicate findings within a SOC environment.

**Learning Objectives:**

* The purpose of alert reporting and escalation.
* How to use the "Five Ws" for professional reporting.
* Best practices for crisis communication and cross-departmental coordination.

Question: I am ready to start!

> **Answer:** No answer needed

***

## Task 2: Alert Funnel

<figure><img src="../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption></figcaption></figure>

The alert funnel describes the lifecycle of an alert as it moves from automated detection to resolution or escalation.

1. **Alert Reporting:** Formally documenting investigation details, especially for True Positives.
2. **Alert Escalation:** Passing complex or high-risk threats to L2 analysts for remediation (e.g., host isolation, malware removal).
3. **Communication:** Interacting with other teams (IT, HR, Legal) to verify activity or gather additional context.

Question: What is the process of passing suspicious alerts to an L2 analyst for review?

> **Answer:** Alert Escalation

Question: What is the process of formally describing alert details and findings?

> **Answer:** Alert Reporting

***

## Task 3: Reporting Guide

A good report follows the **Five Ws** approach to ensure no critical information is missed:

* **Who:** The user, hostname, or IP involved.
* **What:** The specific action (e.g., file download, command execution).
* **When:** The exact timestamp of the activity.
* **Where:** The affected device, cloud environment, or network segment.
* **Why:** The logical reasoning behind your verdict (True/False Positive).

#### Case 1: Phishing Analysis (Microsoft Spoof)

* **Sender:** Microsoft Support `<support@microsoft.com>`
* **Recipient:** Eddie Huffman (`e.huffman@tryhackme.thm`)
* **Evidence:** SPF/DKIM Failures; attached `REPORT.rar` file.

**Draft Report / Analyst Comment:**

> **Who:** Recipient e.huffman@tryhackme.thm (Eddie Huffman, IT Manager).
>
> **What:** Automated analysis flagged a phishing email containing a suspicious .rar attachment titled "Microsoft Teams Pricing Increase."
>
> **When:** Activity detected post-delivery on 07.04.2026.
>
> **Where:** Corporate Mail Gateway / IT Manager's Inbox.
>
> **Why:** **True Positive.** Despite the sender appearing as support@microsoft.com, the SPF and DKIM checks both failed, confirming the sender address is spoofed. The attached `REPORT.rar` file likely contains a malicious payload.

Question: According to the SOC dashboard, which user email leaked the sensitive document?

> **Answer:** c.clark@tryhackme.thm

Question: Looking at the new alerts, who is the "sender" of the suspicious, likely phishing email?

> **Answer:** support@microsoft.com

Question: Using the Five Ws template, what flag did you receive after writing a good report?

> **Answer:** THM{reporting\_is\_easy\_with\_5ws}

***

## Task 4: Escalation Guide

You should escalate an alert if it indicates a major attack, requires remediation actions you cannot perform, or if you need senior technical support.

<figure><img src="../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (21) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Case 2: Spike of Domain Discovery (High Severity)

* **Investigation:** Commands like `whoami /priv`, `net group`, and `nltest` were executed.
* **Context:** Parent process is `revshell.exe`, spawned by `w3wp.exe` (Web Server).
* **Verdict:** **True Positive.**
* **Action:** Escalate to **E.Fleming (L2)** for immediate containment.

**Analyst Comment:**

> **Who:** User NT AUTHORITY\SYSTEM on host DMZ-MSEXCHANGE-2013.
>
> **What:** Execution of a spike of AD discovery commands (whoami, net group, nltest).
>
> **When:** Mar 27th 2025 at 19:56.
>
> **Where:** DMZ Exchange Server.
>
> **Why:** **True Positive.** The commands were executed by `revshell.exe`, which originated from the IIS worker process (`w3wp.exe`). This indicates a successful exploitation of the web server resulting in a reverse shell. The attacker is now performing internal reconnaissance. Immediate host isolation and L2 investigation are required.

Question: Who is your current L2 in the SOC dashboard that you can assign (escalate) the alerts to?

> **Answer:** E.Fleming

Question: What flag did you receive after correctly escalating the alert from the previous task to L2?

> **Answer:** THM{dont\_be\_afraid\_to\_escalate}

Question: After you finish your triage on the second alert, you should receive a flag?

> **Answer:** THM{reporting\_and\_escalation\_expert}

***

## Task 5: SOC Communication

<figure><img src="../.gitbook/assets/image (22) (1) (1).png" alt=""><figcaption></figcaption></figure>

Communication is key during high-stress incidents.

* **If L2 is unavailable:** Use emergency contacts (L3 or Manager).
* **Breached Accounts:** Never contact a user through a platform they are suspected to be compromised on.
* **Overwhelming Alerts:** Inform L2 but continue prioritizing by severity.

Question: Should you first try to contact your manager in case of a critical threat (Yea/Nay)?

> **Answer:** Nay (Try L2/L3 first)

Question: Should you immediately contact your L2 if you think you missed the attack (Yea/Nay)?

> **Answer:** Yea

***

## Conclusion

Alert reporting preserves context, escalation ensures remediation, and communication ensures team coordination. These skills transition an L1 analyst from "alert monitoring" to "incident participation." Mastering the art of professional communication not only secures the organization but also sets the stage for your advancement into more senior analytical roles within the SOC.
