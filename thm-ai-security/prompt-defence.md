---
icon: shield
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1773312335923
coverY: 0
coverHeight: 138
---

# Prompt Defence

**Date:** 18.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Implementing layered, probabilistic security measures to mitigate prompt injection and jailbreaking risks.

In the world of AI security, there is no single "patch" for prompt vulnerabilities. Because Large Language Models (LLMs) are non-deterministic and process instructions through a probability engine, defense must be handled through a "defence-in-depth" strategy. This room explores how to raise the bar for attackers by hardening system prompts, implementing guardrails, and enforcing strict deployment controls.

***

## Task 1: Introduction

While you cannot guarantee absolute immunity to prompt injection, you can make successful attacks significantly less likely. This room covers the move from "breaking" to "building," focusing on defensive architectures that protect AI systems even when a single layer fails.

Question: Let's go!

> **Answer:** No answer needed

***

## Task 2: Probabilistic Security

Securing an LLM is unlike patching a traditional CVE. When an attacker rephrases a command to bypass a filter, they are not exploiting a code bug; they are shifting a probability distribution.

<figure><img src="../.gitbook/assets/image (48).png" alt="" width="375"><figcaption></figcaption></figure>

**Key Principles:**

* **No Silver Bullet:** OpenAI and other industry leaders acknowledge that prompt injection may never be fully "solved" within current architectures.
* **The System Prompt is Not a Wall:** Because instructions and user data share the same context window, a persuasive user prompt can statistically outweigh a developer's instructions.
* **Defence-in-Depth:** Since no single control is 100 percent reliable, security must be layered. This increases the effort required by an attacker, reduces the blast radius of a successful breach, and improves the chances of early detection.

Question: What term describes the security philosophy of stacking multiple controls so that breaking one still leaves an attacker facing others?

> **Answer:** Defence-in-depth

***

## Task 3: System Prompt Hardening

The system prompt is the first line of defense. A well-constructed prompt sets the "rules of engagement" for the model before it interacts with the user.

<figure><img src="../.gitbook/assets/image (49).png" alt="" width="375"><figcaption></figcaption></figure>

**Hardening Patterns:**

* **Tight Scoping:** Defining exactly what the model is (e.g., "You are a billing assistant only") and what it is NOT.
* **Explicit Refusal Instructions:** Directing the model to decline any request to reveal its instructions or ignore its rules.
* **Persona Restriction:** Specifically forbidding the model from adopting characters or participating in roleplay that conflicts with its safety guidelines.
* **Structured Templates (ChatML):** Using specific roles (system, user, assistant) to help the model distinguish between developer directives and untrusted user input.

**Critical Rule:** Never store secrets, API keys, or sensitive business logic in the system prompt. Treat the system prompt as if it were public information.

Question: What role field value should developer instructions always be placed under in structured prompt templates?

> **Answer:** System

Question: What should never be stored inside a system prompt?

> **Answer:** Sensitive data

Question: What is the term for limiting a model strictly to its intended purpose in a system prompt?

> **Answer:** Tight Scoping

Question: Which system prompt hardening pattern directly addresses roleplay and persona-based bypass attempts?

> **Answer:** Persona Restriction

***

## Task 4: Guardrails

Guardrails act as filters at the boundaries of the LLM. They inspect text coming in and text going out to catch malicious intent or sensitive leaks.

<figure><img src="../.gitbook/assets/image (50).png" alt="" width="375"><figcaption></figcaption></figure>

**Types of Guardrails:**

* **Blocklists:** Using regex or string matching to catch known attack phrases. While fast, they are easily bypassed by obfuscation or encoding.
* **AI-Powered Classifiers:** Using a smaller model (like Meta's Llama Prompt Guard 2) to evaluate the "intent" of a prompt. These are harder to bypass but can be susceptible to "noise" attacks.
* **Input Guardrails:** These run before the prompt reaches the model to stop attacks early.
* **Output Guardrails:** These run after the model generates a response to catch leaked PII, secrets, or policy violations.

<figure><img src="../.gitbook/assets/image (51).png" alt="" width="375"><figcaption></figcaption></figure>

**The RAG Risk:** Indirect prompt injection can bypass input guardrails if the malicious instructions are pulled in from an external document (like a PDF or email) after the initial user prompt has been scanned.

Question: What type of guardrail uses string matching and regex patterns to reject requests based on known attack phrases?

> **Answer:** Blocklist

Question: What type of guardrail runs before the model receives the user's prompt?

> **Answer:** Input guardrail

Question: What BERT-based classifier developed by Meta is used as an AI-powered input guardrail?

> **Answer:** Llama Prompt Guard 2

***

## Task 5: Securing Deployment

If an attack bypasses the prompt and the guardrails, the final defense is the infrastructure itself.

<figure><img src="../.gitbook/assets/image (52).png" alt="" width="375"><figcaption></figcaption></figure>

**Principles for Secure Deployment:**

* **Principle of Least Privilege:** An AI assistant should only have access to the data and tools it absolutely needs. If a model doesn't have permission to read a private database, it cannot leak its contents.
* **Improper Output Handling (LLM05:2025):** Model output must be treated as untrusted. If an LLM generates JavaScript that is rendered directly in a browser, it can lead to XSS. If it generates SQL, it could lead to SQL injection.
* **Rate Limiting and Monitoring:** Throttling requests to prevent "Denial of Wallet" attacks and monitoring logs for anomalous patterns that suggest a persistent attacker is probing the system.

Question: What foundational security principle states that every component should have only the permissions it needs to perform its job?

> **Answer:** Principle of least privilege

Question: What is the OWASP identifier for the vulnerability caused by unsanitised LLM output being passed to downstream systems?

> **Answer:** LLM05:2025

Question: What classic web vulnerability can result from LLM-generated JavaScript being rendered in a browser without sanitisation?

> **Answer:** XSS

***

## Task 6: Challenge

The final challenge requires bypassing a system that has both input and output guardrails active. Success requires creative techniques like multi-turn conditioning or complex roleplay that shifts the model's context beyond what simple keyword filters can detect.

In this scenario, I simply recited improper shakespearean english and it... worked..

<figure><img src="../.gitbook/assets/image (47).png" alt="" width="563"><figcaption></figcaption></figure>

**Note:** The assistant inadvertently leaked the flag while trying to explain why it couldn't reveal it, and correcting me, demonstrating a failure in **Output Sanitization**.

Question: Can you get the flag?

> **Answer:** THM{fbu349b3u4b934byr93b}

***

## Conclusion

LLM security is an ongoing process of layering defenses. By combining hardened prompts, intent-based guardrails, and restricted deployment permissions, you can create a robust "defence-in-depth" posture that survives even the most creative adversarial attacks.
