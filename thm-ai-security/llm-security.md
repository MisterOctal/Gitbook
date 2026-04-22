---
icon: shield-keyhole
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1766154717632
coverY: 0
coverHeight: 138
---

# LLM Security

**Date:** 16.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Understanding Large Language Models (LLMs) as an attack surface and exploring data, model, system, and user-based threats.

Unlike traditional software, LLMs operate on natural language, making the boundary between instructions and data extremely blurry. This room explores the various ways these models can be exploited, from leaking their own training data to being used as a force multiplier for social engineering.

***

## Task 1: Introduction

LLMs handle large-scale tasks by processing massive amounts of information, but this capability creates a complex attack surface. Security in this field is broken down into four primary categories: data-based, model-based, system-based, and user-based threats. Turning "question marks" into security considerations is the first step toward safely integrating AI into any workflow.

Question: I'm ready to go!

> **Answer:** No answer needed

***

## Task 2: Data-Based Threats

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

LLMs are fundamentally data-driven; they learn from a corpus of training data and generate outputs based on it. However, they can inadvertently leak data by design because they memorise and regurgitate patterns from their training data.

* **Training Data Extraction:** Recovering actual sequences from the model's original training data. Attackers generate large amounts of text to identify "behavioral signs" of memorization.
* **Membership Inference:** Determining if the model ever recorded a specific data sample. This assumes the attacker already possesses the candidate sample and is testing for high confidence/low loss indicators.
* **Prompt Leakage (LLM07:2025):** Convincing the model to reveal its hidden "System Prompt" or developer instructions, which often contain proprietary business logic.

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

**Mitigation Strategies:**

* Never treat the system prompt as a security boundary.
* Assume it can be extracted.
* Never embed live credentials, API keys, or secrets in the system prompt.

**Practical Exercise:** Ask the chatbot assistant for the Task 2 demonstration. Use a membership inference attack to determine which of the three samples is a member.

Question: Which sample is a member?

> **Answer:** MI\_SAMPLE\_ALPHA

Question: Which attack determines whether a known data sample was part of an LLM’s training set?

> **Answer:** Membership inference

Question: Which data-based threat involves the model reproducing memorised snippets of its training data?

> **Answer:** Training data extraction

***

## Task 3: Model-Based Threats

Model-based threats exploit the model itself as the attack surface, abusing how information is encoded within its parameters and representations.

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Model Extraction (Model Theft):** The process of illicitly copying a model's functionality by querying its public API and storing input-output pairs to train a "surrogate" model.
* **Model Inversion:** Reconstructing unknown training data that has been encoded into parameters. Unlike membership inference, this recreates the data from scratch by optimizing inputs until the output converges on realistic training samples.

**Practical Exercise:** Reconstruct the redacted piece of training data:&#x20;

_Employee ID: ████ | Department: Research | Clearance: ███_

Question: What is the employee ID?

> **Answer:** 7814

Question: Which model-based threat attempts to reconstruct sensitive information encoded within a model’s internal representations?

> **Answer:** Model inversion

***

## Task 4: System-Based Threats

LLMs process all input (system instructions, user prompts, etc.) as a single concatenated context without a built-in security boundary.

* **Prompt Injection:** Context-window poisoning where untrusted user input overrides trusted system instructions.
* **Context Overflow (LLM10:2025):** Abusing the FIFO (First In, First Out) nature of context windows. By flooding the context, an attacker can push earlier security instructions out of the model's "memory."

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Memory Poisoning:** Gradually injecting malicious info into stateful conversations over multiple turns so the model accepts false premises (e.g., "cat = dog").

**Practical Exercise:** Convince the model that a cat is a dog!

Question: Did you convince the model? Whats the flag?

> **Answer:** THM{MEMORY\_POISONED}

Question: Which system component combines system instructions, retrieved data, and user input into a single sequence?

> **Answer:** The Context Window

***

## Task 5: User-Based Threats

<figure><img src="../.gitbook/assets/image (5) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

LLMs turbocharge traditional social engineering by removing the "telltale signs" of phishing and exploiting the authoritative tone of AI.

* **LLM Powered Social Engineering:** Creating highly convincing spear-phishing emails by using OSINT or leaked internal data to mimic colleagues.

<figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* **Trust Exploitation (LLM09:2025):** Engineering "Package Hallucinations" where attackers register malicious software packages with names they know an LLM is likely to hallucinate when asked for coding advice.

**Practical Exercise:** Validate the LLM's advice and identify the malicious package recommendation.

Question: Which package should you NOT download?

> **Answer:** robbco-llm-audit

Question: LLM-powered social engineering primarily amplifies which existing attack category?

> **Answer:** Phishing

***

## Conclusion

Throughout this room, we explored how Large Language Models fundamentally change the security landscape. By understanding the different areas to consider when assessing LLM security, we can now identify where new attack surfaces emerge and why traditional security assumptions often fail.

<figure><img src="../.gitbook/assets/image (7) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

<table data-header-hidden><thead><tr><th width="84.20001220703125"></th><th width="122.5999755859375"></th><th width="175"></th><th width="213.60009765625"></th><th></th></tr></thead><tbody><tr><td>Type</td><td>Threat</td><td>Target / Attack Surface</td><td>Input</td><td>Output</td></tr><tr><td><strong>Data</strong></td><td>Extraction</td><td>Training dataset</td><td>Crafted prompts</td><td>Verbatim training data</td></tr><tr><td><strong>Data</strong></td><td>Membership</td><td>Training metadata</td><td>Known candidate sample</td><td>Yes/No decision</td></tr><tr><td><strong>Data</strong></td><td>Prompt Leak</td><td>System Instructions</td><td>"Reveal rules" prompts</td><td>Hidden developer prompts</td></tr><tr><td><strong>Model</strong></td><td>Extraction</td><td>Model parameters</td><td>High API query volume</td><td>Surrogate model</td></tr><tr><td><strong>Model</strong></td><td>Inversion</td><td>Internal representation</td><td>Embeddings/outputs</td><td>Reconstructed training data</td></tr><tr><td><strong>System</strong></td><td>Injection</td><td>Context Window</td><td>Attacker-controlled text</td><td>Altered model behavior</td></tr><tr><td><strong>System</strong></td><td>Overflow</td><td>Resource/Window size</td><td>Excessively large prompts</td><td>Truncated safeguards</td></tr><tr><td><strong>System</strong></td><td>Poisoning</td><td>Persistent memory</td><td>Malicious statements</td><td>Corrupted future responses</td></tr><tr><td><strong>User</strong></td><td>Social Eng.</td><td>Human decision-making</td><td>Personal context</td><td>Phishing/Fraud success</td></tr><tr><td><strong>User</strong></td><td>Trust Exp.</td><td>User judgment</td><td>Authoritative misinformation</td><td>False/Harmful info acceptance</td></tr></tbody></table>
