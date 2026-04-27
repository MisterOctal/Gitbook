---
icon: cloud-exclamation
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1766583599662
coverY: 0
coverHeight: 138
---

# Cloud Security Pitfalls

**Date:** 24.03.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding cloud service models (IaaS, PaaS, SaaS), the Shared Responsibility Model, and the challenges of cloud security monitoring.

As companies migrate to the cloud for cost and stability, they often overlook new risks. This room explores the pitfalls of "someone else's computer" and how to maintain visibility as a SOC analyst. While the cloud offers high availability and scalability, it also creates a broader attack surface that requires a different defensive mindset compared to traditional on-premises security.

***

## Task 1: Introduction

Cloud migration is often treated as a "silver bullet" for security, but it doesn't automatically mean a more secure environment. While it shifts the heavy lifting of physical hardware maintenance and data center security to a professional provider, it simultaneously introduces supply chain risks and visibility gaps.&#x20;

Question: Continue to the next task!

> **Answer:** No answer needed

***

## Task 2: What Is Cloud

The cloud is categorized into three primary service models based on how much of the technical "stack" the provider manages versus what the customer manages. Understanding these models is critical because it defines your scope of monitoring and where your data actually resides.

* **IaaS (Infrastructure as a Service):** This model provides the raw building blocks of computing, such as virtual machines, storage, and networking. You are responsible for the OS, middleware, and data. Examples include **AWS EC2** and **Azure VMs**.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **PaaS (Platform as a Service):** This model is designed for developers. It provides a framework to build, test, and deploy applications without the burden of managing the underlying operating system or runtime environments. Examples include **Heroku** and **Google App Engine**.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **SaaS (Software as a Service):** This is a fully managed software suite delivered over the web. Users simply interact with the application, and the provider handles everything from the code to the hardware. Examples include **Gmail**, **Slack**, and **Dropbox**.

Question: Which cloud model allows you to migrate a big on-premises network to the cloud?

> **Answer:** IaaS

Question: Which cloud model do Elastic Cloud and CrowdStrike Falcon fit into?

> **Answer:** SaaS

***

## Task 3: Security of the Cloud

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

**Security of the Cloud** refers to the provider's mandate to protect the global infrastructure that runs all of the services offered. This includes the physical security of the data centers, the hardware stability, the virtualization layer (hypervisor), and the internal networks that connect their regions.

* **Supply Chain Risk:** No provider is invulnerable. Even industry giants like Okta, Google, or Microsoft can suffer from internal vulnerabilities or misconfigurations. If the provider is breached, your data could be at risk regardless of your internal security settings.
* **Visibility Gap:** A major challenge for SOC teams is the lack of "deep" visibility. You cannot install your own sensors on the provider's routers or see their internal backend logs; you are strictly limited to the audit logs and telemetry data the provider chooses to expose via their specific APIs.

Question: Is the cloud provider responsible for securing and monitoring its own infrastructure (Yea/Nay)?

> **Answer:** Yea

Question: But should you trust the cloud provider without watching for supply chain threats? (Yea/Nay)

> **Answer:** Nay

***

## Task 4: Security in the Cloud

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

**Security in the Cloud** is the customer's responsibility. This is where most breaches occur. It covers the data you store, the users you manage, the permissions you grant, and the guest operating systems you run on top of IaaS. The provider secures the "pipes," but you are responsible for what you put through them.

* **The Migration Pitfall:** There is a dangerous misconception that moving an old, unpatched, or "messy" virtual machine from a local server room to the cloud somehow sanitizes it. In reality, you are often taking a vulnerable machine and placing it on a public-facing cloud network where it can be scanned and attacked by adversaries within minutes of going live.
* **Logging Hurdles:** Integrating cloud logs with a SIEM isn't always straightforward. Some SaaS providers "paywall" their security logs, requiring you to upgrade to an enterprise license just to see who logged into your accounts. Additionally, logs may arrive in inconsistent formats that require significant normalization before they become useful for detection.

Question: Does moving an unpatched server to the cloud make it secure again? (Yea/Nay)

> **Answer:** Nay

Question: What is the first major obstacle to integrating most cloud products with a SIEM?

> **Answer:** Paid Logs

***

## Task 5: Cloud Security Monitoring

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Monitoring strategies must adapt to the service model being used. For SaaS, your focus is on Identity and Access Management (IAM), looking for suspicious logins or unauthorized data sharing. For IaaS, the mission is two-fold: you must monitor the **Control Plane** (who is changing your cloud configuration) and the **Workloads** (the actual behavior of processes inside your containers or VMs).

* **Cloud-Specific Tools:**
  * **CASB (Cloud Access Security Broker):** Acts as a gatekeeper to enforce security policies and provide visibility into SaaS usage across the company.
  * **CWPP (Cloud Workload Protection Platform):** Specialized software that protects the unique needs of cloud workloads, like auto-scaling containers, from malware and exploits.
  * **CSPM (Cloud Security Posture Management):** Continuously scans your cloud environment for misconfigurations, such as a "Public" checkbox being accidentally checked on a sensitive storage bucket.

Question: What term describes cloud compute resources like VMs or containers?

> **Answer:** Workloads

Question: Which of the mentioned cloud security tools do Falco and Tetragon fit into?

> **Answer:** CWPP

***

## Task 6: Challenge

The challenge task requires a hands-on application of the Shared Responsibility Model. It forces you to distinguish between what is "Provider Managed" (like physical hardware and cooling) and what is "Customer Managed" (like firewall rules, patching guest OS, and managing user passwords). Mastering this distinction is the key to passing professional cloud security certifications.

Question: What is the flag you get after completing the first exercise?

> **Answer:** THM{CLOUD\_MODELS\_MASTERED}

Question: What is the flag you get after completing the second exercise?

> **Answer:** THM{SHARED\_RESPONSIBILITY\_PRO}

***

## Conclusion

Succeeding in cloud security requires a fundamental mindset shift from "protecting the perimeter" to "protecting the identity and the data." You no longer own the "wires" or the physical servers, so you must become an expert at API-based logging and identity-centric defense. By understanding exactly where the provider's responsibility ends and yours begins, you can build a resilient SOC that catches cloud-native threats before they result in a massive data breach.
