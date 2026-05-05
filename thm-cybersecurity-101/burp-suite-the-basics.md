---
icon: screwdriver-wrench
cover: >-
  https://cdn.discordapp.com/attachments/1031890307979149332/1488119454192893994/image.png?ex=69cb9ebd&is=69ca4d3d&hm=b6bef076cd313e056306586d19c947ccbd3879ae7e55b0e9adf5debcac1d6119
coverY: 0
coverHeight: 138
---

# Burp Suite: The Basics

**Date:** 30.03.2026

**Room Category:** Walkthrough

**Core Concept:** Mastering the industry-standard web application security testing framework for interception and analysis.

Burp Suite is a Java-based framework designed to be a comprehensive solution for conducting web application penetration testing. By acting as a transparent proxy, it enables security researchers to intercept, view, and modify traffic between their browser and the target server. This capability is the backbone of modern web security assessments, including mobile applications and API testing.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

Welcome to the foundational room for Burp Suite. This module transitions from theoretical concepts to the practical application of the tool. I will cover the installation process, the various editions available (Community vs. Professional), and the core navigation required to move through the suite's complex interface.

Question: Let us start!

> **Answer:** No answer needed

***

## Task 2: What is Burp Suite?

Burp Suite is the industry-standard tool for hands-on security assessments. It captures and enables the manipulation of all HTTP/HTTPS traffic. While we focus on the **Community Edition** (free for non-commercial use), it is important to understand the Professional and Enterprise versions. The Professional edition adds automated scanning and removes rate limits on tools like Intruder, while the Enterprise edition is designed for continuous, server-side scanning of entire web estates.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Which edition of Burp Suite runs on a server and provides constant scanning for target web apps?

> **Answer:** Burp Suite Enterprise

Question: Burp Suite is frequently used when attacking web applications and \_\_\_\_\_\_ applications.

> **Answer:** Mobile

***

## Task 3: Features of Burp Community

Even without a paid license, the Community edition offers a powerful array of tools. The **Proxy** is the most famous, allowing for real-time interception. The **Repeater** allows you to "repeat" a specific request multiple times with manual modifications, perfect for testing SQL injection or IDOR vulnerabilities. Other tools like **Decoder** (for data transformation), **Comparer** (for finding differences between responses), and **Sequencer** (for testing token randomness) round out a toolkit that can handle almost any manual testing scenario.

Question: Which Burp Suite feature allows us to intercept requests between ourselves and the target?

> **Answer:** Proxy

Question: Which Burp tool would we use to brute-force a login form?

> **Answer:** Intruder

***

## Task 4: Installation

Burp Suite is a Java application, meaning it can run on Windows, macOS, and Linux as long as a Java Runtime Environment is present. On platforms like Kali Linux and the THM AttackBox, Burp comes pre-installed and ready to use. For personal machines, PortSwigger provides dedicated installers that include a bundled "private" browser, which simplifies the configuration process significantly by eliminating the need to manually install CA certificates in your standard browser.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: If you have chosen not to use the AttackBox, ensure that you have a copy of Burp Suite installed before proceeding.

> **Answer:** No answer needed

***

## Task 5: The Dashboard

The Dashboard is your "Mission Control." It is divided into quadrants that help you monitor background activity. The **Tasks** quadrant shows what Burp is doing behind the scenes (like passive crawling), while the **Event Log** provides a running list of technical actions, such as starting the proxy listener or encountering connection errors.&#x20;

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

For Professional users, the **Issue Activity** and **Advisory** sections display live results from the automated vulnerability scanner.

Question: What menu provides information about the actions performed by Burp Suite, such as starting the proxy, and details about connections made through Burp?

> **Answer:** Event log

***

## Task 6: Navigation

Navigating Burp Suite requires getting used to its "tab-within-a-tab" structure. The top row of tabs switches between major modules (Proxy, Target, Repeater), while a second row of sub-tabs appears beneath them for module-specific views (e.g., Proxy Intercept vs. Proxy History). If the interface feels cramped, you can "detach" tabs into separate windows. Learning keyboard shortcuts like `Ctrl + Shift + R` for Repeater will significantly speed up your testing workflow.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: Which tab Ctrl + Shift + P will switch us to?

> **Answer:** Proxy tab

***

## Task 7: Options

Settings in Burp are split into **Global (User) settings** and **Project settings**. User settings apply to the entire installation, such as your theme (Dark Mode!) or font sizes. Project settings are specific to the current engagement. Since the Community edition does not support saving project files, these settings must be reconfigured each time you start a new session. The "Search" feature in the settings menu is the fastest way to find specific toggles like the "Cookie Jar" or "Hotkeys."

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: In which category can you find a reference to a "Cookie jar"?

> **Answer:** Sessions

Question: In which base category can you find the "Updates" sub-category, which controls the Burp Suite update behaviour?

> **Answer:** Suite

Question: What is the name of the sub-category which allows you to change the keybindings for shortcuts in Burp Suite?

> **Answer:** Hotkeys

Question: If we have uploaded Client-Side TLS certificates, can we override these on a per-project basis (yea/nay)?

> **Answer:** yea

***

## Task 8: Introduction to the Burp Proxy

The Proxy is the core of the framework. It sits between you and the server, holding requests in "limbo" until you decide to forward or drop them. This allows you to bypass client-side validation by changing data after it leaves the browser but before it reaches the server. Even when "Intercept is off," Burp maintains an **HTTP History** log, which is a vital record of every request and response that has passed through the proxy for later analysis.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Click me to proceed to the next task.

> **Answer:** No answer needed

***

## Task 9: Connecting through the Proxy (FoxyProxy)

To effectively use Burp, your browser traffic must be routed through it. FoxyProxy is a browser extension that simplifies this by allowing you to switch between proxy profiles with one click. By setting up a profile pointing to `127.0.0.1:8080`, you tell your browser to send all requests to Burp Suite first. This task walks through the configuration of the proxy IP and port, ensuring that your environment is properly staged for interception.

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Click me to proceed to the next task.

> **Answer:** No answer needed

***

## Task 10: Site Map and Issue Definitions

The **Target** tab is essential for organization. The **Site Map** builds a hierarchical tree of every asset discovered during your session, which is invaluable for visualizing the attack surface. While the Community Edition doesn't have an active scanner, the **Issue Definitions** provide a library of vulnerabilities that serve as an excellent educational resource and a checklist for manual testing. Understanding how to use **Scope** settings here ensures you don't accidentally attack assets you aren't authorized to test.

Question: What is the flag you receive after visiting the unusual endpoint?

> **Answer:** THM{NmNlZTliNGE1MWU1ZTQzMzgzNmFiNWVk}

***

## Task 11: The Burp Suite Browser

PortSwigger recently introduced a built-in Chromium browser to solve the "proxy configuration headache." This browser is pre-configured to trust Burp’s certificate and route all traffic through the proxy automatically. It saves time and prevents technical friction, though users running as root on Linux may need to disable the sandbox in settings to get it running. It is the recommended way for beginners to start their first interception.

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="445"><figcaption></figcaption></figure>

Question: Click me to proceed to the next task.

> **Answer:** No answer needed

***

## Task 12: Scoping and Targeting

As you browse the web, your Burp history can become cluttered with background traffic from other tabs or system updates. **Scoping** allows you to tell Burp: "Only care about this specific domain." By adding a target to the scope and configuring the Proxy to only intercept in-scope requests, you create a much cleaner workspace. This prevents the frustration of having to "Forward" a hundred unrelated Google or analytics requests just to see one target interaction.

Question: Add http://10.113.174.248/ to your scope and change the proxy settings to only intercept traffic to in-scope targets.

See the difference between the amount of traffic getting caught by the proxy before and after limiting the scope.

> **Answer:** No answer needed

***

## Task 13: Proxying HTTPS

When you visit a site over HTTPS, the browser expects a certificate from a trusted authority. Since Burp intercepts this traffic, it presents its own certificate, which the browser initially distrusts. To fix this, you must download the Burp CA certificate (usually from `http://burp`) and manually import it into your browser's "Trusted Root Certification Authorities" store. This allows you to inspect encrypted traffic just as easily as plain HTTP.

Question: If you are not using the AttackBox, configure Firefox (or your browser of choice) to accept the PortSwigger CA certificate for TLS communication through the Burp Proxy.

> **Answer:** No answer needed

***

## Task 14: Example Attack

Bypassing client-side validation is a classic Burp use case. Many websites use JavaScript to "validate" input (like checking for a valid email format) before the request is sent. However, since we can intercept the request _after_ it passes that JS check, we can inject malicious payloads like an XSS script directly into the request body. This task demonstrates how to intercept a form submission, modify the data, URL-encode it for safety, and forward it to trigger an alert box on the server side.

Question: Click me to proceed to the next task.

> **Answer:** No answer needed

***

## Task 15: Conclusion

You have now completed the essentials of Burp Suite. You've moved from basic installation to performing a live (simulated) attack. The Proxy is the foundation, but the journey continues with the **Repeater** and **Intruder** rooms, where you will learn how to automate and refine your testing. Keep practicing by browsing local VMs through Burp to see how modern web applications communicate "under the hood."
