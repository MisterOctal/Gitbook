---
icon: bee
cover: https://assets.tryhackme.com/additional/banners/sP6d0iZ.png
coverY: 0
coverHeight: 138
---

# OWASP Top 10 2025 Part 3

**Date:** 05.04.2026

**Room Category:** Walkthrough

**Core Concept:** Analyzing vulnerabilities related to application behavior and user input, specifically focusing on Cryptographic Failures (A04), Injection (A05), and Integrity Failures (A08).

This room explores how applications handle data throughout its lifecycle from receiving user input to storing it securely and verifying its integrity. Failures in these areas often lead to complete system compromise or sensitive data exposure.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (24).png" alt="" width="375"><figcaption></figcaption></figure>

This room introduces three key elements of the OWASP Top 10 (2025) list that relate to how an application processes and trusts data:

* **A04:** Cryptographic Failures
* **A05:** Injection
* **A08:** Software or Data Integrity Failures

Question: I'm ready!

> **Answer:** No answer needed

***

## Task 2: A04: Cryptographic Failures

Cryptographic failures occur when sensitive data is not adequately protected. This is often due to missing encryption, weak algorithms, or poor key management.

**Common Issues:**

* **Rolling Your Own Crypto:** Using custom, unvetted algorithms instead of industry standards.
* **Weak Hashing:** Using MD5 or SHA1 for passwords instead of slow, robust functions like bcrypt, scrypt, or Argon2.
* **Hard-coded Secrets:** Storing API keys or encryption keys directly in the source code.
* **Insecure Transmission:** Failing to use TLS for data in transit.

**Prevention:**

* Use strong, modern, and vetted cryptographic libraries.
* Implement secure key management systems (KMS).
* Ensure all sensitive data at rest and in transit is encrypted.

Question: Decrypt the encrypted notes. One of them will contain a flag value. What is it?

> **Answer:** THM{WEAK\_CRYPTO\_FLAG}

***

## Task 3: A05: Injection

Injection is a classic vulnerability that occurs when an application improperly handles user input, passing it directly to a system (database, shell, etc.) that executes it as a command.

**Types of Injection:**

* **SQL Injection:** Inserting malicious SQL queries into input fields.
* **Command Injection:** Executing arbitrary OS commands on the server.
* **SSTI (Server Side Template Injection):** Abusing template engines to execute code.
* **AI Prompts:** Manipulating AI models through specialized input.

**Prevention:**

* **Parameterized Queries:** Use prepared statements for SQL to separate code from data.
* **Input Validation:** Enforce strict data types and sanitization (escaping dangerous characters).
* **Safe APIs:** Use APIs that do not invoke the system shell directly.

Question: Perform an SSTI attack on the practical. You need to read the contents of flag.txt that is located within the same directory as the web application.

> **Answer:** THM{SSTI\_FLAG\_OBTAINED}

***

## Task 4: A08: Software or Data Integrity Failures

Integrity failures happen when an application trusts code, updates, or data without verifying its origin or authenticity.

**Common Issues:**

* **Insecure Deserialization:** Accepting serialized objects from untrusted sources, which can lead to Remote Code Execution (RCE).
* **Unsigned Updates:** Installing software updates without verifying cryptographic signatures.
* **CI/CD Breaches:** Malicious code being injected into the build pipeline.

**Prevention:**

* **Integrity Checks:** Use digital signatures or checksums (hashes) to verify data.
* **Trust Boundaries:** Never assume data is safe; verify every piece of information that impacts application logic.
* **Secure Deserialization:** Avoid deserializing data from untrusted sources or use safe serialization formats (like JSON) instead of binary formats like Python's `pickle`.

Question: Use Python to pickle a malicious, serialised payload that reads the contents of flag.txt and submits it to the application. What are the contents of flag.txt?

> **Answer:** THM{INSECURE\_DESERIALIZATION}

***

## Conclusion

Insecure data handling remains a top threat because it targets the core functionality of web applications. By implementing strong cryptography, preventing injection through parameterization, and strictly verifying data integrity, developers can build significantly more resilient systems.
