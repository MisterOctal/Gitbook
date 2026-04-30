---
icon: swords
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1759510976709
coverY: 0
coverHeight: 138
---

# Humans as Attack Vectors

**Date:** 06.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding why humans are considered the "weakest link" in cyber security and how social engineering exploits psychology to bypass technical defenses.

Even the most secure technical fortress can be compromised if a "gatekeeper" (a human user) is manipulated into opening the door. Attackers target humans because it is often easier to trick a person into providing access than it is to exploit a complex software vulnerability.

***

## Task 1: Introduction

The SOC (Security Operations Center) protects more than just servers; it protects the people within the organization. This room explores modern attacker methods that target the human element.

**Learning Objectives:**

* The role of humans in the cyber security chain.
* How the SOC detects and mitigates human-centric attacks.
* Realistic scenarios involving phishing and security policy.

Question: I am ready to learn!

> **Answer:** No answer needed

***

## Task 2: The Human Element

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="150"><figcaption></figcaption></figure>

Humans are targeted because of the **access** they hold. Whether it's an HR manager's database access or an IT administrator's VPN credentials, a single compromised account can lead to a full-scale breach.

| Attack Example           | Goal                               |
| ------------------------ | ---------------------------------- |
| Breach HR Google Account | Steal/sell employee database.      |
| Trick wealthy user       | Hijack web banking session.        |
| Breach IT Admin VPN      | Access the core corporate network. |

Question: What or who is the weakest link in cyber security?

> **Answer:** Humans

Question: What do attackers seek when targeting humans in a cyberattack?

> **Answer:** Access

***

## Task 3: Attacks on Humans

**Social Engineering** is the tactic of manipulating human psychology (trust, fear, urgency, or curiosity) to achieve a goal.

Some common methods of social engineering are:

* **Phishing:** Malicious emails (approx. 3.4 billion sent daily) leading to fake login pages or containing malware.

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/678ecc92c80aa206339f0f23/room-content/678ecc92c80aa206339f0f23-1752707092322.svg" alt=""><figcaption></figcaption></figure>

* **Malware Downloads:** Using fake CAPTCHAs, QR codes, or SEO poisoning to trick users into installing malicious software.

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Deepfakes:** Using AI-generated audio or video to impersonate trusted figures (e.g., a boss requesting a wire transfer).
* **Impersonation:** Pretending to be IT support or another department over a phone call to gain account control.

Question: What is the name of an attack tactic that manipulates human psychology?

> **Answer:** Social Engineering

Question: Which social engineering method is about pretending to be someone else?

> **Answer:** Impersonation

***

## Task 4: Defending Humans

Defense relies on two pillars: **Mitigation** (preventing the attack) and **Detection** (identifying an attack that has already bypassed initial defenses).

#### Key Mitigation Measures:

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Anti-phishing solutions:** Tools that block malicious emails before they reach the inbox.
* **Antivirus/EDR:** Prevents malware from executing even if a user downloads it.
* **Security Awareness Training:** Teaching employees to recognize and report suspicious activity.
* **"Trust but Verify":** A policy of double-checking suspicious requests via a secondary communication channel.

Question: Which process is aimed at preventing or reducing the chance of an attack?

> **Answer:** Mitigation

Question: Which mitigation measure is about training employees in cyber security?

> **Answer:** Security Awareness Training

***

## Task 5: Practice

As a SOC analyst, you must often step outside the SIEM to interact with other teams (HR/IT) or propose company-wide policy changes.

**Scenario Results:**

* **Employees at Risk:** Identifying and protecting targeted coworkers.
* **Security Policy:** Implementing higher-level changes to reduce the attack surface.

Question: What flag did you receive after completing the "Employees at Risk" challenge?

> **Answer:** THM{anyone\_else\_at\_risk?}

Question: What flag did you receive after completing the "Security Policy" challenge?

> **Answer:** THM{human\_protection\_expert!}

***

## Conclusion

Since cybersecurity is a continuous process, staying informed is critical for a SOC analyst. Following sites like _Krebs on Security_, _The Hacker News_, and _BleepingComputer_ helps you stay ahead of evolving social engineering trends.
