---
icon: head-side-gear
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1775757044838
coverY: 0
coverHeight: 138
---

# AI Threat Modelling

**Date:** 16.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Auditing enterprise AI/ML risks using STRIDE, MITRE ATLAS, and OWASP.

AI is already embedded in enterprise operations, from support bots to fraud detection. This widespread adoption creates a unique attack surface involving non-deterministic outputs, poisoned training data, and prompt injections that traditional security frameworks weren't originally built to address.

***

## Task 1: Introduction

This room focuses on evaluating and documenting AI threats. We move beyond "black box" thinking to a systematic defender-focused auditing process.

**The Scenario:** As a MegaCorp analyst, we must assess three key deployments: a RAG-based customer chatbot, a product recommendation engine, and an automated fraud detection system.

Question: I understand the learning objectives and am ready to learn about AI threat modelling!

> **Answer:** No answer needed

***

## Task 2: AI-Specific Assets and Attack Surfaces

AI introduces unique assets that must be identified to avoid missing critical risks.

<figure><img src="../.gitbook/assets/image (241).png" alt=""><figcaption></figcaption></figure>

**Key AI Assets:**

* **Training Data:** The foundation of model behavior. Poisoning here corrupts the model permanently.
* **Model Weights / Parameters:** The "secret sauce." Theft allows an attacker to run a functional copy of your AI.
* **Embedding Vectors:** Used for similarity and retrieval in RAG pipelines.
* **System Prompts:** Instructions defining model persona and constraints.
* **Feature Stores:** Data repositories feeding real-time inputs.
* **Model Registry / Artifacts:** Versions ready for deployment; a key point for supply chain attacks.

**AI Differentiators:**

* **Non-deterministic behavior:** The same input may yield different outputs.
* **The black box problem:** Deep neural networks lack easy logical explainability.

Question: In a RAG-based system, which AI asset type is used to retrieve relevant context at query time?

> **Answer:** Embedding Vectors

Question: An attacker gains access to MegaCorp's model registry and swaps the production model for a modified version. Which AI-specific asset has been compromised?

> **Answer:** Model Registry / Artifacts

***

## Task 3: Data Supply Chain and STRIDE's Gaps

AI systems add a complex data supply chain to the traditional software supply chain.

**The 5-Stage AI Data Supply Chain:**

<figure><img src="../.gitbook/assets/image (243).png" alt=""><figcaption></figcaption></figure>

1. **Data Collection:** Gathering raw data from various sources.
2. **Cleaning and Labelling:** Filtering and assigning metadata.
3. **Model Training:** Embedding patterns into model weights.
4. **Validation and Packaging:** Evaluating and storing model versions.
5. **Inference:** Serving predictions in production.

**Why STRIDE Alone Falls Short:**

<figure><img src="../.gitbook/assets/image (244).png" alt=""><figcaption></figcaption></figure>

* **Tampering** in AI (poisoning) is often delayed and diffuse, unlike immediate logic errors.
* **Adversarial manipulation** often spans multiple STRIDE categories.
* **Elevation of Privilege** takes on new meaning when a model can execute code or browse the web.

Question: An attacker injects crafted data points into a training pipeline over several months, gradually shifting the model's decision boundaries. At which supply chain stage does the attacker inject the malicious data?

> **Answer:** Data Collection

Question: Which STRIDE category is insufficient for capturing the delayed, diffuse effects of training data poisoning?

> **Answer:** Tampering

***

## Task 4: Adapting STRIDE for AI Systems

<figure><img src="../.gitbook/assets/image (245).png" alt=""><figcaption></figcaption></figure>

We adapt the STRIDE categories to fit AI-specific contexts.

1. **Spoofing:** Injecting fake data into knowledge bases to fool RAG systems.
2. **Tampering:** Data poisoning to shift model decision boundaries.
3. **Repudiation:** Lack of decision audit trails due to non-deterministic outputs.
4. **Information Disclosure:** **Model Extraction** (stealing the model via API queries).
5. **Denial of Service:** **Denial of Wallet** (forcing expensive, resource-heavy queries).
6. **Elevation of Privilege:** **Jailbreaking** to bypass safety guardrails.

Question: What is the primary AI-specific manifestation of Information Disclosure in the STRIDE-AI mapping?

> **Answer:** Model Extraction

Question: An attacker crafts prompts that cause an LLM to bypass its safety guidelines and content restrictions. Which STRIDE category does this map to?

> **Answer:** Elevation of Privilege

Question: Which OWASP LLM Top 10 (2025) entry addresses the risks of AI systems being granted too many permissions or too much autonomy?

> **Answer:** LLM06: 2025 - Excessive Agency

Question: An attacker drives your monthly inference bill from $15,000 to $180,000 without taking your service offline. What is this type of attack commonly called?

> **Answer:** Denial of Wallet

***

## Task 5: MITRE ATLAS

ATLAS is a matrix of specific adversary tactics and techniques tailored for AI.

<figure><img src="../.gitbook/assets/image (246).png" alt=""><figcaption></figcaption></figure>

**Key Techniques:**

* **Data Poisoning (AML.T0020):** Manipulating training pipelines.
* **Model Extraction (AML.T0024):** Reconstructing a model copy via API.
* **Evade ML Model (AML.T0015):** Crafting adversarial data for misclassification.
* **LLM Prompt Injection (AML.T0051):** Manipulating behavior via prompts.
* **Backdoor ML Model (AML.T0018):** Embedding hidden triggers during training.

Question: What does the acronym ATLAS stand for?

> **Answer:** Adversarial Threat Landscape for Artificial-Intelligence Systems

Question: Which ATLAS case study described a self-replicating prompt injection worm that spread between AI agents via RAG email systems?

> **Answer:** Morris II

Question: What is the ATLAS technique ID for Model Extraction?

> **Answer:** AML.T0024

***

## Task 6: OWASP LLM Top 10

The OWASP list helps map specific risks to architectural components.

* **LLM Inference Endpoint:** High risk for injection, leakage, and excessive agency.
* **Vector Database / RAG:** Vulnerable to indirect injection and misinformation.
* **Training Pipeline:** The primary focus for supply chain and poisoning risks (LLM03/04).

Question: How many of the OWASP LLM Top 10 entries affect the LLM Inference Endpoint?

> **Answer:** 6

Question: An organisation notices their chatbot is rendering LLM output directly in the browser without sanitisation. Which OWASP entry does this fall under?

> **Answer:** Improper Output Handling

Question: Which component in a typical LLM architecture is the primary one that needs hardening against data and model supply chain risks (LLM03)?

> **Answer:** Training Pipeline

***

## Task 7: Practical Assessment

In this practical exercise, you must assess the **MegaCorp AI Assistant**, a RAG-enabled chatbot. The assessment focuses on mapping 5 specific OWASP LLM Top 10 (2025) vulnerabilities to observed system behaviors.

Question: What's the flag?

> **Answer:** THM{AI\_THREAT\_MODEL\_COMPLETE}

***

## Conclusion

AI threat modeling combines **STRIDE** (categories), **ATLAS** (techniques), and **OWASP** (architectural mapping) to create an actionable security assessment. This repeatable workflow ensures that even as AI evolves, the methodology for securing it remains consistent.
