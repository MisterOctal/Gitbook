---
icon: bee
cover: https://assets.tryhackme.com/additional/banners/sP6d0iZ.png
coverY: 0
coverHeight: 138
---

# OWASP Top 10 2025 Part 2

**Date:** 05.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding how failures in architecture, system configuration, and logic lead to vulnerabilities like Security Misconfigurations, Software Supply Chain Failures, Cryptographic Failures, and Insecure Design.

This room focuses on "weak foundations." Unlike code-level bugs, these vulnerabilities often stem from how a system is built, configured, or integrated with third-party components and AI models.

Side note: I'm not gonna lie, I had to look at youtube walkthroughs and other people's writeups for this room, it was pretty confusing and weirdly worded in my opinion.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (24) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

This room covers four specific categories from the OWASP Top 10 (2025) that relate to architectural failures:

* **AS02:** Security Misconfigurations
* **AS03:** Software Supply Chain Failures
* **AS04:** Cryptographic Failures
* **AS06:** Insecure Design

Question: I am ready to learn about design flaw vulnerabilities!

> **Answer:** No answer needed

***

## Task 2: AS02: Security Misconfigurations

Security misconfigurations occur when systems are deployed with unsafe defaults or exposed services. These are deployment mistakes rather than code bugs.

**Common Patterns:**

* Default credentials left unchanged.
* Unnecessary services (like telnet or admin panels) exposed to the internet.
* Verbose error messages leaking system details (stack traces).
* Outdated software with known vulnerabilities.

**Prevention:**

* Harden default configurations and remove unused features.
* Hide stack traces from end-users.
* Regularly audit cloud permissions (S3 buckets, etc.).

Question: What's the flag?

> **Answer:** THM{V3RB0S3\_3RR0R\_L34K}

***

## Task 3: AS03: Software Supply Chain Failures

These failures happen when an application relies on compromised or unverified third-party libraries, APIs, or AI models. If a dependency is malicious, your entire application is compromised.

**Common Patterns:**

* Using unmaintained or unverified libraries.
* Automatically installing updates without signature verification.
* Insecure CI/CD pipelines that allow code tampering.

**Prevention:**

* Verify all third-party components before use.
* Use software composition analysis (SCA) to monitor for vulnerable dependencies.
* Lock down the build process to prevent unauthorized changes.

Question: What's the flag?

> **Answer:** THM{SUPPLY\_CH41N\_VULN3R4B1L1TY}

***

## Task 4: AS04: Cryptographic Failures

Cryptographic failures involve the incorrect use (or lack) of encryption. This leads to the exposure of sensitive data like passwords or PII.

**Common Patterns:**

* Using weak/deprecated algorithms (MD5, SHA-1).
* Hard-coded encryption keys or secrets in the source code.
* Sending sensitive data over unencrypted channels (HTTP).
* Poor key rotation practices.

**Prevention:**

* Use modern algorithms (AES-GCM, TLS 1.3).
* Use dedicated secret management services (AWS KMS, HashiCorp Vault).
* Never hard-code keys; rotate them regularly.

Question: What's the flag?

> **Answer:** THM{CRYPTO\_FAILURE\_H4RDCOD3D\_K3Y}

***

## Task 5: AS06: Insecure Design

Insecure design is a broad category representing flaws in the system's logic or architecture. These cannot be "patched" easily because the flaw is in the workflow itself.

**In the AI Era:**

* **Prompt Injection:** When user input hijacks the logic of an LLM.
* **Blind Trust:** Acting on AI output without human-in-the-loop validation.
* **Assumptions:** Assuming only certain devices (like mobile apps) will call an API, while neglecting backend authentication.

**Prevention:**

* Implement threat modeling during the design phase.
* Apply the Principle of Least Privilege.
* Treat all model/user inputs as untrusted.

Question: What's the flag?

> **Answer:** THM{1NS3CUR3\_D35IGN\_4SSUMPT10N}

***

## Conclusion

Security cannot be an afterthought. Strong systems require clear security requirements and vetted dependencies from the start. "Getting the design right early" is the most effective way to avoid preventable incidents.
