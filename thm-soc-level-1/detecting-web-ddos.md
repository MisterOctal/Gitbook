---
icon: shield
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/616945d482ef350052080da1-1756793603975
coverY: 0
coverHeight: 138
---

# Detecting Web DDoS

**Date:** 28.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Exploring Layer 7 (Application Layer) Denial-of-Service attacks, attacker motives, log-based detection, SIEM investigation, and mitigation strategies using CDNs and WAFs.

Denial-of-Service (DoS) attacks aim to disrupt or completely block access to a website or web service. This research focuses on how DoS and DDoS attacks target the application layer, the techniques behind them, and how defenders can detect and mitigate these common threats.

***

## Task 1: Introduction

When web services face a denial-of-service attack, customers cannot log in, shop, or access services, resulting in lost revenue and trust. The primary objective is to maintain availability while filtering out malicious traffic.

Question: I understand the learning objectives and am ready to embark on a Denial-of-Service adventure!

> **Answer:** No answer needed

***

## Task 2: DoS and DDoS Attacks

DoS attacks can be launched against different layers of a system. The focus here is the application layer (Layer 7) of the OSI model.

#### Denial-of-Service (DoS)

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

A basic DoS attack relies on a single machine and internet connection. Its impact is capped by local CPU, memory, bandwidth, and network limitations. However, it can still be highly effective if it targets specific vulnerabilities. For example, a search form that fails to validate input properly can be abused by submitting malformed data that causes the application to hang or crash. Flooding this form with requests, or even a single massive request, can cause an outage.

#### Distributed Denial-of-Service (DDoS)

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

To scale up, attackers utilize botnets: armies of compromised devices (computers, IoT devices, servers) under their control. When instructed, the bots flood the target website, overwhelming its resources much more effectively than a single machine could.

#### Types of Denial-of-Service Attacks

* **Slowloris:** Sending many partial HTTP requests to tie up server resources.
* **HTTP Flood:** Sending a massive number of HTTP requests to overwhelm the server.
* **Cache Bypass:** Bypassing CDN edge servers (often using random query strings) and forcing the origin server to respond.
* **Oversized Query:** Forcing the server to process large, resource-intensive requests.
* **Login/Form Abuse:** Overloading authentication logic with login attempts or password resets.
* **Faulty Input Validation Abuse:** Exploiting poorly designed input handling logic to stall the application.

Question: What class of attack relies on disrupting the availability of a web service?

> **Answer:** Denial-of-Service

Question: What do we call the network of compromised machines that attackers use to launch DDoS attacks?

> **Answer:** Botnet

***

## Task 3: Attack Motives

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="251"><figcaption></figcaption></figure>

While a short web service outage may seem minor, for organizations depending on constant availability, the consequences are severe.

#### Possible Attack Motives

* **Financial Loss:** Disrupt services to stop sales and revenue (e.g., flooding an e-commerce site during holiday sales).
* **Extortion:** Demand payment to stop a current attack (e.g., threatening a bank with a ransom DDoS).
* **Hacktivism:** Disruption for social or political protest (e.g., attacking government websites).
* **Distraction:** Redirect defenders' attention while other attacks (like data exfiltration) take place.
* **Competition:** Disrupt a rival's service to drive up their costs or gain market share.
* **Denial of Wallet:** Force the victim to rack up service usage costs (e.g., repeatedly accessing AWS S3 data to generate costs per request).
* **Reputational Damage:** Cause customers to lose trust in a company (e.g., crashing game servers on launch day).

#### In the Wild

* **BBC (2015):** A DDoS attack by "New World Hacking" took the site offline simply as a test of their capabilities.
* **Microsoft (2023):** Experienced a large-scale Layer 7 DDoS attack causing Azure, OneDrive, and Outlook outages. Hacktivist group Anonymous Sudan claimed responsibility, utilizing HTTP flooding and Slowloris techniques.

Question: Which attacker motive aims to make customers lose confidence in a company?

> **Answer:** Reputational Damage

Question: Which motive most likely drove the 2023 DDoS attack against Microsoft?

> **Answer:** Hacktivism

***

## Task 4: Log Analysis

Web server logs (Apache, NGINX, IIS) are valuable sources of evidence. Examining these logs uncovers patterns that distinguish between normal user traffic and malicious floods.

#### Key Indicators of DoS/DDoS

* **High Request Rate:** A resource-heavy page (like `/login`) is flooded with requests. Each request triggers database queries, rapidly exhausting resources.
* **Odd User-Agents:** Spoofed, outdated, or unusual User-Agents (e.g., `curl/7.6.88` or `Python-urllib/3.x`) point to automated scripts.
* **Geographic Anomalies:** Traffic originating globally rather than from expected regional user bases indicates a distributed botnet.
* **Burst Timestamps:** A sudden spike of requests packed into the exact same second points to automation.
* **Server Errors (5xx):** A sudden surge of 503 Service Unavailable errors indicates the server is maxed out and struggling.
* **Logic Abuse:** Queries designed to overload the server (e.g., `GET /products?limit=999999`).

#### Targeted Resources

Attackers focus on endpoints consuming the most server resources per request:

* `/login`: Involves complex authentication processes.
* `/search`: Requires resource-intensive database queries.
* `/api endpoints`: Critical for dynamic content delivery.
* `/register` or `/signup`: Requires database writes and validation.
* `/contact` or `/feedback`: Requires database entries and email generation.
* `/cart` or `/checkout`: Requires session management, inventory checks, and payment processing.

#### Log Investigation (`access.log`)

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

By analyzing the provided access log, specific malicious patterns emerge from the noise of legitimate traffic.

Question: What is the attacker’s IP address?

> **Answer:** 203.12.23.195

Question: Which page is repeatedly targeted by the attacker’s requests?

> **Answer:** /login

Question: After the attack, what error code do legitimate users receive?

> **Answer:** 503

***

## Task 5: Leveraging SIEMs

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Security Information and Event Management (SIEM) platforms, such as Splunk, make log analysis significantly more efficient by combining multiple log sources and extracting queryable fields (e.g., `clientip`, `useragent`, `uri`). Visualizations like timecharts allow analysts to instantly identify massive request spikes.

#### Splunk Investigation

In this scenario, Splunk is utilized to investigate a distributed attack originating from multiple IPs.

Question: What was the most frequently requested uri?

> **Answer:** /search

Question: Which clientip made the most requests to the target uri?

> **Answer:** 203.0.113.7

Question: How many IP addresses were part of the botnet that attacked your website?

> **Answer:** 60

Question: Which useragent was most commonly used by the attacking traffic?

> **Answer:** Java/1.8.0\_181

Question: Use the timechart command to visualize the requests. What is the peak number of requests made per second during the attack?

> **Answer:** 207

Question: Which legitimate (non-attacking) clientip received the first 503 response status post-attack?

> **Answer:** 10.10.0.27

***

## Task 6: Defense

Preventing and mitigating denial-of-service attacks requires a multi-layered approach.

#### Application Level Defense

* **Secure Development Practices:** Search fields and forms must strictly validate input to prevent abuse. Strict rules on query length and character types prevent the backend from getting bogged down processing malformed data.
* **Challenges:** Requiring a challenge before granting access blocks bots. This includes visual CAPTCHAs or invisible JavaScript challenges running in the background to confirm human interaction.

#### Network and Infrastructure Defenses

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt="" width="390"><figcaption></figcaption></figure>

* **Content Delivery Network (CDN):** CDNs cache and serve content from edge servers globally. This reduces latency and absorbs massive traffic spikes before they hit the origin server. CDNs also provide load-balancing to distribute traffic across servers, ensuring no single server fails.
* **Web Application Firewall (WAF):** Integrated within CDNs, WAFs inspect incoming traffic and enforce rules based on threat intelligence and rate-limiting. For example, a custom rule might block an IP if it requests `/login.php` more than five times per minute.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Bypassing Security Measures

Attackers constantly attempt to bypass CDNs and WAFs. Common techniques include:

* **Cache Busting:** Appending random query parameters to URLs (e.g., `/products?a=abcd`) to bypass the CDN cache and force the origin server to generate a new response.
* **Evasion:** Changing user agents, spoofing referrers, and launching requests from diverse geographic regions to circumvent WAF rules.

Question: What type of security challenge blocks bots by asking users to solve a simple puzzle?

> **Answer:** CAPTCHA

Question: Which CDN feature spreads traffic across multiple servers to prevent overload?

> **Answer:** Load-balancing

***

## Conclusion

Detecting and defending against DoS and DDoS requires understanding attacker motives, analyzing high-resource endpoint targeting, recognizing log anomalies, and deploying robust CDN and WAF infrastructures to absorb large-scale disruptions.
