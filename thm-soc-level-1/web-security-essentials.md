---
icon: shield
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/616945d482ef350052080da1-1754137100965
coverY: 0
coverHeight: 138
---

# Web Security Essentials

**Date:** 27.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding web infrastructure, common security risks, and defensive measures (WAF, CDN, Hardening) for modern web applications.

Web applications are among the most common entry points for attackers because they are always available and exposed. This room covers the transition from desktop to web, the request-response cycle, and the layers of protection required to secure a web service.

***

## Task 1: Introduction

Websites are critical assets for banking, shopping, and communication. Defending them requires a deep understanding of web infrastructure.

Question: I'm ready to go!

> **Answer:** No answer needed

***

## Task 2: Why Web?

<figure><img src="../.gitbook/assets/image (8).png" alt="" width="563"><figcaption></figcaption></figure>

The shift from desktop to web (ongoing since the 1990s) was driven by cloud computing and SaaS. While this increases accessibility and updates, it expands the attack surface.

**Trade-offs:**

* **Owner Risks:** Must secure the app 24/7; globally accessible; responsible for user data.
* **User Risks:** Data may be stored insecurely; browser breaches compromise all accounts; identity theft.

**Real-World Examples:**

* **Equifax (2017):** 150M Americans' data compromised via an Apache Struts vulnerability.
* **Capital One (2019):** 100M customers' data exposed due to a misconfigured WAF.

Question: Have applications shifted from desktop to web over the past couple of decades (Yea/Nay)?

> **Answer:** Yea

Question: Who is ultimately responsible for ensuring the security of users' data within a web application?

> **Answer:** Web App Owner

***

## Task 3: Web Infrastructure

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Web services function via a request-response cycle using three main components:

1. **Application:** The code (PHP, JS), images, and styles.
2. **Web Server:** Hosts the app and handles requests (Apache, Nginx, IIS).
3. **Host Machine:** The underlying OS (Linux/Windows).

**Common Web Servers:**

<figure><img src="../.gitbook/assets/image (2) (1).png" alt="" width="386"><figcaption></figcaption></figure>

* **Apache:** Most popular for blogs/WordPress.
* **Nginx:** High-performance standard used by Netflix and GitHub.
* **IIS:** Microsoft's enterprise-grade web server.

Question: What does your web browser send to a server to receive a web page?

> **Answer:** Request

Question: What web server is most commonly used to host WordPress websites?

> **Answer:** Apache

Question: What do we call the OS and environment that runs the web server and application?

> **Answer:** Host Machine

***

## Task 4: Protecting the Web

Securing a web service requires a defense-in-depth approach across all three layers.

**Security Best Practices:**

* **Application:** Secure coding, input validation/sanitization, and role-based access control.
* **Web Server:** Logging (access logs), WAF, and CDN usage.
* **Host Machine:** Principle of Least Privilege, system hardening, and antivirus.
* **Universal:** Patch management and strong authentication.

**Logging:** Access logs track the client IP, timestamp, requested resource, status code, and user agent.

* **GET:** Retrieve a resource.
* **POST:** Submit data (like credentials).

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Question: What cyber security concept involves stopping or limiting damage from threats?

> **Answer:** Mitigation

Question: What security control involves ensuring all software and components are up to date?

> **Answer:** Patch Management

***

## Task 5: Defense Systems

#### Content Delivery Network (CDN)

<figure><img src="../.gitbook/assets/image (5) (1).png" alt="" width="563"><figcaption></figcaption></figure>

CDNs use edge servers to reduce latency and act as a buffer.

* **Benefits:** IP Masking, DDoS protection, enforced HTTPS, and integrated WAFs (Cloudflare, AWS CloudFront).

#### Web Application Firewall (WAF)

<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

WAFs inspect HTTP traffic and block harmful requests based on rules.

* **Types:** Cloud-based (Reverse Proxy), Host-based (on the server), and Network-based (physical appliance).
* **Detection Methods:** \* _Signature-Based:_ Matches known patterns (e.g., sqlmap user agent).
  * _Heuristic-Based:_ Analyzes context (e.g., long query strings with special characters).
  * _Behavioral:_ Flags anomalies (e.g., brute force attempts).

#### Antivirus (AV)

Primarily protects the **Host Machine** (endpoint protection). It detects malicious file uploads like web shells that an attacker might try to place on the server after bypassing application-layer security.

Question: Which type of Web Application Firewall operates by running on the same system as the application itself?

> **Answer:** Host-Based

Question: Which common WAF detection technique works by matching incoming requests against known malicious patterns?

> **Answer:** Signature-Based

***

## Task 6: Practice Scenario - Secure-A-Site

<figure><img src="../.gitbook/assets/image (7) (1).png" alt="" width="390"><figcaption></figcaption></figure>

In this scenario, we apply hardening across the three layers of the "Secure-A-Site" application by answering a quiz.

1. **Web Application Layer:** Implementing input sanitization and secure coding practices.
2. **Web Server Layer:** Configuring WAF rules and enabling verbose access logging.
3. **Host Machine Layer:** Disabling unused services/ports and applying the principle of least privilege.

Question: What flag did you receive for securing the Web Application?

> **Answer:** THM{web\_app\_secured!}

Question: What flag did you receive for securing the Web Server?

> **Answer:** THM{server\_security\_expert!}

Question: What flag did you receive for securing the Host Machine?

> **Answer:** THM{the\_final\_security\_layer!}

***

## Conclusion

Web security is an ongoing process of defending the request-response cycle. By understanding infrastructure components and layering defenses like WAFs and CDNs, we can significantly reduce the risk of high-impact breaches.
