---
icon: head-side-virus
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1775302016511
coverY: 0
coverHeight: 138
---

# AI Models & Data

**Date:** 14.04.2026

**Room Category:** Walkthrough

**Core Concept:** Analyzing the data supply chain and model-building decisions to understand the hidden security risks that exist before an AI model is ever deployed.

While most security discussions focus on how an AI behaves at runtime, this room explores the "upstream" risks. Every AI model is a product of its training data; decisions made during scraping, filtering, and fine-tuning create permanent security implications: including embedded PII and eroded safety filters: that cannot be easily "patched" out.

***

## Task 1: Introduction

This room moves beyond the high-level ML lifecycle to examine the specific risks of data provenance, the "black box" nature of model weights, and the legal/security liabilities inherited when using third-party base models.

Question: I understand the learning objectives and am ready to learn about AI models and data!

> **Answer:** No answer needed

***

## Task 2: Training Data

<figure><img src="../.gitbook/assets/image (189).png" alt="" width="369"><figcaption></figcaption></figure>

Large Language Models require trillions of tokens, typically sourced from web scraping, licensed datasets, synthetic data, and internal corpora. Because this data is collected at such a massive scale, the "trust profile" is often low.

#### Data Provenance & The ML-BOM

<figure><img src="../.gitbook/assets/image (190).png" alt="" width="375"><figcaption></figcaption></figure>

**Data Provenance** is the ability to track the origin, collection time, and modification history of data. Currently, over 70% of datasets have unspecified or mislabeled licenses. To combat this, the industry is moving toward the **ML-BOM** (Machine Learning Bill of Materials), a documented inventory of dataset sources and filtering decisions.

#### The PII Problem

<figure><img src="../.gitbook/assets/image (191).png" alt="" width="375"><figcaption></figcaption></figure>

Web scraping tools like **Common Crawl** often sweep up sensitive information. Scans of these archives have revealed thousands of live API keys and passwords. Once these are ingested during training, they become baked into the model weights, allowing attackers to potentially "prompt" the model into revealing credentials near-verbatim.

Question: What term describes the ability to answer where data came from, when it was collected, and whether it has been modified?

> **Answer:** Data Provenance

Question: What is the name of the most widely used public corpus that underpins essentially every major model family?

> **Answer:** Common Crawl

Question: What is the AI equivalent of a Software Bill of Materials (SBOM), used to document dataset sources, licenses, and filtering decisions?

> **Answer:** ML-BOM

***

## Task 3: Building the Model

The process of turning raw data into a functional model involves several technical decisions that impact security.

#### Overfitting and Validation

<figure><img src="../.gitbook/assets/image (192).png" alt="" width="375"><figcaption></figcaption></figure>

An **Epoch** is one complete pass through a dataset. Training for too many epochs leads to **Overfitting**: where the model memorizes the training data instead of learning general patterns. This increases the risk of the model leaking sensitive training examples. To prevent this, a **Validation Set** is held back to test how well the model generalizes to unseen data.

#### Optimization & Federated Learning

<figure><img src="../.gitbook/assets/image (193).png" alt="" width="375"><figcaption></figcaption></figure>

* **Pruning:** Removing unimportant parameters to shrink the model.
* **Quantization:** Reducing the numerical precision of weights (e.g., 32-bit to 8-bit). This can silently degrade safety filters.
* **Federated Learning:** Training across decentralized devices. While better for privacy (raw data stays local), it introduces the risk of **Poisoned Local Updates**, where a participant submits malicious gradients to skew the global model.

Question: What term describes one complete pass of the training algorithm through the entire dataset?

> **Answer:** Epoch

Question: What problem occurs when a model memorises training data rather than learning general patterns?

> **Answer:** Overfitting

Question: What post-training optimisation technique reduces the numerical precision of model weights to cut memory and compute requirements?

> **Answer:** Quantisation

Question: What training approach trains a model across decentralised devices, sending only weight updates rather than raw data to a central server?

> **Answer:** Federated Learning

***

## Task 4: The Inheritance Problem

<figure><img src="../.gitbook/assets/image (194).png" alt="" width="375"><figcaption></figcaption></figure>

Most organizations do not train models from scratch. They use **Pre-trained Models** and perform **Fine-tuning** (training a base model on a smaller, specific dataset).

#### The Inheritance Risks

<figure><img src="../.gitbook/assets/image (195).png" alt="" width="375"><figcaption></figcaption></figure>

Fine-tuning only changes task-specific behavior; it does not "cleanse" the base model.

1. **Safety Erosion:** Stanford research shows safety filters can be broken with as few as 10 adversarial examples during fine-tuning.
2. **Increased Attack Surface:** Specialized models are often more susceptible to prompt injection.
3. **Version Persistence:** If a base model checkpoint contains a backdoor, every fine-tuned derivative inherits that vulnerability.

Question: What is the process of taking a pre-trained model and continuing to train it on a smaller, task-specific dataset?

> **Answer:** Fine-tuning

Question: What term describes a model that has already been trained on a large general-purpose dataset?

> **Answer:** Pre-trained Model

***

## Task 5: The Black Box Problem

<figure><img src="../.gitbook/assets/image (196).png" alt="" width="375"><figcaption></figcaption></figure>

A model's **Weights** (billions of floating-point numbers) are not human-readable. Unlike source code, you cannot "audit" a model to see exactly why it made a specific decision.

#### Model Cards

To provide transparency, developers use **Model Cards**. These act as "nutritional labels" for AI, documenting:

* Training data and filtering.
* Intended vs. unintended use cases.
* Bias assessments and known limitations.

Question: What documentation artifact accompanies a model to describe what it is, how it was built, and where it falls short?

> **Answer:** Model Card

Question: What are the billions of floating-point numbers that make up a trained model collectively referred to as?

> **Answer:** Weights

***

## Task 6: Practical - Model Audit

In the practical lab, we performed a scavenger hunt for red flags in a simulated model repository. Auditing a third-party model requires looking past the marketing and into the documentation gaps.

Screenshots of the static lab:

<figure><img src="../.gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

#### Audit Findings & Red Flags

<table data-header-hidden><thead><tr><th width="195.60003662109375"></th><th width="86.60003662109375"></th><th></th></tr></thead><tbody><tr><td>Finding</td><td>Severity</td><td>Significance</td></tr><tr><td><strong>Vague Training Data Source</strong></td><td>High</td><td>No provenance or checksums means no way to verify if data was tampered with upstream.</td></tr><tr><td><strong>No PII Filtering Mention</strong></td><td>High</td><td>Large-scale scraping sweeps up PII; without filtering, these secrets are baked into the weights.</td></tr><tr><td><strong>"Custom" Licence (No Terms)</strong></td><td>Medium</td><td>Creates legal uncertainty; you cannot assess operational risk without accessible terms.</td></tr><tr><td><strong>Undocumented File Size/Mod</strong></td><td>Medium</td><td>Pruning/Quantisation changes behavior post-training and can silently degrade safety mechanisms.</td></tr><tr><td><strong>Specific Unchecked Version</strong></td><td>Medium</td><td>Every derivative inherits backdoors or issues present in the specific base model checkpoint.</td></tr><tr><td><strong>Sparse Evaluation/No Red-Teaming</strong></td><td>Medium</td><td>Limits what can be concluded about security resilience; absence of evidence is a warning sign.</td></tr></tbody></table>

Question: Complete the exercise to get the flag!

> **Answer:** THM{A\_m0del\_Stud3nt}

***

## Conclusion

#### Analyst Perspective: Looking Beneath the Surface

The security of an AI system is largely determined before the first user prompt is ever typed. Understanding the data supply chain is critical: without an ML-BOM or a thorough Model Card, an organization is essentially deploying a "black box" with unknown liabilities.

As we move toward more specialized AI, the "Inheritance Problem" becomes a primary concern. You aren't just deploying your team's fine-tuning work; you are deploying the billions of weights shaped by an invisible and often unaudited data supply chain. Identifying gaps in model cards like the lack of adversarial testing or vague licensing is what separates a reckless deployment from a secure one.
