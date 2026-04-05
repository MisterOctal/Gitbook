---
icon: bee
cover: https://assets.tryhackme.com/additional/banners/sP6d0iZ.png
coverY: 0
coverHeight: 138
---

# OWASP Top 10 2025 Part 1

**Date:** 05.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding how failures in Identity, Authentication, Authorisation, and Accountability (IAAA) lead to critical web vulnerabilities like Broken Access Control and Authentication Failures.

This room focuses on the implementation of the IAAA model within web applications. When these four pillars are not properly enforced, it creates opportunities for attackers to bypass security boundaries, impersonate users, or act without being recorded.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (24).png" alt="" width="375"><figcaption></figcaption></figure>

This room covers three specific categories from the OWASP Top 10 (2025) that stem from IAAA implementation failures:

* **A01:** Broken Access Control
* **A07:** Authentication Failures
* **A09:** Logging & Alerting Failures

Question: I am ready to learn about IAAA failures!

> **Answer:** No answer needed

***

## Task 2: What is IAAA?

IAAA is a sequential model for verifying users and their actions. You cannot skip a level; each depends on the success of the previous one.

1. **Identity:** The unique claim of who a person or service is (e.g., a username).
2. **Authentication:** Proving that identity (e.g., via passwords or MFA).
3. **Authorisation:** Determining what an identity is permitted to do (e.g., user vs. admin).
4. **Accountability:** Logging actions to prove who did what and when.

Question: What does IAAA stand for?

> **Answer:** Identity, Authentication, Authorisation, Accountability

***

## Task 3: A01: Broken Access Control

Broken Access Control occurs when the server fails to verify if a user is authorized for a specific request. This often manifests as:

* **Horizontal Privilege Escalation:** Accessing the data of another user with the same role (e.g., User A viewing User B's profile).
* **Vertical Privilege Escalation:** A lower-privileged user performing actions reserved for higher-privileged users (e.g., a standard user accessing an admin panel).
* **IDOR (Insecure Direct Object Reference):** Changing a parameter like `?id=101` to `?id=102` to see unauthorized data.

Question: If you don't get access to more roles but can view the data of another users, what type of privilege escalation is this?

> **Answer:** Horizontal

To find the flag in this task, we manually change our user id in the url bar, upon switching to user number 7, we find the flag:

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Question: What is the note you found when viewing the user's account who had more than $ 1 million?

> **Answer:** THM{Found.the.Millionare!}

***

## Task 4: A07: Authentication Failures

These failures occur when the application cannot reliably confirm a user's identity. Common weaknesses include:

* **Username Enumeration:** Differences in error messages that reveal if a username exists.
* **Weak Passwords:** Lack of complexity requirements or brute-force protection.
* **Logic Flaws:** Errors in the registration or password reset flows (e.g., registering "aDmiN" to conflict with "admin").

To find the flag in this task, we register an account by the username aDmiN, then we add a password to it. Afterwards, we can access the administrator account using the password we registered to access the flag:

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

Question: What is the flag on the admin user's dashboard?

> **Answer:** THM{Account.confusion.FTW!}

***

## Task 5: A09: Logging & Alerting Failures

Without proper logging, security events cannot be detected or investigated. Accountability relies on logs that record "who, what, when, and where." Failures include:

* Not logging failed login attempts.
* Storing logs locally where an attacker can delete them.
* Failing to alert security staff during an active brute-force attack.

In this task, we examine a SIEM dashboard. After identifying a string of suspicious logs, we can earn our answers:

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

Question: It looks like an attacker tried to perform a brute-force attack, what is the ip of the attacker?

> **Answer:** 203.0.113.45

Question: Looks like they were able to gain access to an account! What is the username associated with that account?

> **Answer:** admin

Question: What action did the attacker try to do with the account? List the endpoint the accessed.

> **Answer:** /supersecretadminstuff

***

## Conclusion

To secure an application against these failures, developers should:

1. **A01:** Perform server-side authorization checks on every single request.
2. **A07:** Use unique indexes for usernames and enforce rate-limiting.
3. **A09:** Centralize logs and ensure the entire authentication lifecycle is recorded.
