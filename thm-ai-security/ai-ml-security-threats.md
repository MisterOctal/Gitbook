---
icon: head-side-virus
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1744795093190
coverY: 0
coverHeight: 139
---

# AI/ML Security Threats

**Date:** 14.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the foundational layers of Artificial Intelligence (AI), Machine Learning (ML), and Deep Learning (DL), and exploring how these technologies introduce new attack surfaces while providing powerful defensive capabilities.

AI is no longer a futuristic concept; it is actively reshaping the threat landscape. This room breaks down the "AI Boom," explaining the transition from basic algorithms to Transformer-based Large Language Models (LLMs), and how adversaries leverage them for enhanced phishing, malware, and deepfakes.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (173).png" alt="" width="258"><figcaption></figcaption></figure>

The cybersecurity industry is at a turning point as AI becomes central to both attack and defense. This room serves as a foundational entry point into the mechanics of AI/ML, how it shifts the role of security professionals, and how to identify vulnerabilities inherent in the models themselves.

Question: I'm ready to learn about AI/ML security threats!

> **Answer:** No answer needed

***

## Task 2: The Building Blocks of AI

<figure><img src="../.gitbook/assets/image (174).png" alt="" width="375"><figcaption></figcaption></figure>

AI refers to machine systems capable of tasks requiring human reasoning, problem-solving, or creativity. While the field dates back to the 1950s, the emergence of **Machine Learning (ML)**: the ability for computers to learn from data without explicit instructions: marked the first major leap toward modern applications.

#### The Machine Learning Lifecycle

<figure><img src="../.gitbook/assets/image (175).png" alt="" width="375"><figcaption></figcaption></figure>

ML development is an iterative process designed to ensure models are reliable and accurate:

1. **Problem Definition:** Determining the specific goal (e.g., "Is this email spam?").
2. **Data Preparation:** Collecting and cleaning data. This includes **feature engineering** to extract patterns and avoiding **overfitting** (where a model is too familiar with training data and fails to generalize to new, raw data).
3. **Training & Tuning:** Using an algorithm to build the model and optimizing it to minimize errors.
4. **Deployment & Monitoring:** Running the model in production and triggering retraining if accuracy drops over time.

#### ML Algorithm Components

<figure><img src="../.gitbook/assets/image (177).png" alt="" width="375"><figcaption></figcaption></figure>

Every ML algorithm consists of three key mathematical pillars:

* **Decision Process:** Logic that makes predictions based on input data.
* **Error Function:** Evaluates performance by providing feedback on how far off a prediction was.
* **Model Optimization:** Fine-tuning the algorithm to minimize those errors.

#### Learning Categories

* **Supervised Learning:** Trained on labeled data (e.g., predicting house prices).
* **Unsupervised Learning:** Finds hidden patterns in unlabeled data (e.g., clustering similar network traffic).
* **Semi-supervised Learning:** Combines a small amount of labeled data with a large portion of unlabeled data to guide the process.
* **Reinforcement Learning:** Mimics human trial-and-error by rewarding correct decisions and penalizing mistakes.

#### Neural Networks & Deep Learning (DL)

Neural networks replicate the biological behavior of the human brain, using **neurons** (nodes) connected by **synapses** (weighted connections).

<figure><img src="../.gitbook/assets/image (176).png" alt="" width="375"><figcaption></figcaption></figure>

* **Input Layer:** Receives raw data (e.g., pixels from an image).
* **Hidden Layers:** Process and refine the input. Early layers detect simple edges; deeper layers combine them into complex patterns.
* **Output Layer:** Provides the final prediction.

When a network has more than three layers, it is classified as **Deep Learning**. Unlike standard ML, DL is "scalable ML": it can process massive, unstructured datasets without human intervention for labeling, making it effectively self-learning.

<figure><img src="../.gitbook/assets/image (178).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What category of machine learning combines both labelled and unlabelled data?

> **Answer:** semi-supervised learning

Question: What is the first layer in a neural network that handles incoming raw data?

> **Answer:** input layer

Question: Which learning method does not require human-labeled data and can extract features from raw, unstructured input?

> **Answer:** deep learning

Question: What are the weighted connections between nodes in a neural network meant to simulate in the human brain?

> **Answer:** synapses

***

## Task 3: LLMs

<figure><img src="../.gitbook/assets/image (179).png" alt="" width="375"><figcaption></figcaption></figure>

Large Language Models (LLMs) like ChatGPT are advanced Deep Learning models built on **Transformer** architectures. They predict the next word in a sequence based on billions of parameters.

#### Key Concepts

* **Pre-training:** Processing massive datasets using **unsupervised** learning.
* **Backpropagation:** An algorithm used during training to adjust internal parameters based on how far off a prediction was from the actual word.
* **Transformers (2017):** Introduced "attention," allowing the model to process text in parallel and understand context.
* **RLHF:** Reinforcement Learning from Human Feedback: fine-tuning the model using human reviewers to ensure safety and helpfulness.

<figure><img src="../.gitbook/assets/image (180).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What type of AI model enabled major advancements in ChatGPT and similar tools?

> **Answer:** large language models

Question: What is the first training stage where an LLM processes massive amounts of data?

> **Answer:** pre-training

Question: What type of neural network introduced by Google in 2017 powers modern LLMs?

> **Answer:** transformer

***

## Task 4: AI Security Threats

<figure><img src="../.gitbook/assets/image (181).png" alt="" width="138"><figcaption></figcaption></figure>

Adversaries use AI in two ways: attacking the models themselves or using AI to supercharge traditional attacks.

#### Vulnerabilities in AI Models

<figure><img src="../.gitbook/assets/image (182).png" alt="" width="188"><figcaption></figcaption></figure>

* **Prompt Injection:** Using clever prompts to bypass safety filters or leak instructions.
* **Data Poisoning:** Corrupting training data so the model develops a specific "blind spot."
* **Model Theft:** Querying an API until you can build a clone model.
* **Privacy Leakage:** Accidentally revealing PII included in the training set.
* **Model Drift:** The drop in performance over time as real-world data changes.

#### Enhanced Attacks

<figure><img src="../.gitbook/assets/image (183).png" alt="" width="175"><figcaption></figcaption></figure>

* **Enhanced Malware:** Using AI to write, debug, and obfuscate malicious code. This allows low-skill attackers to generate complex malware or create "polymorphic" code that changes its signature to evade antivirus.

<figure><img src="../.gitbook/assets/image (184).png" alt="" width="188"><figcaption></figcaption></figure>

* **Deepfakes:** Realistic voice/video synthesis for bypassing MFA or social engineering.

<figure><img src="../.gitbook/assets/image (185).png" alt="" width="69"><figcaption></figcaption></figure>

* **Phishing:** Using Generative AI to create perfect, fluent emails that lack the usual "broken English" red flags.

Question: What framework was developed by MITRE to guide the understanding of AI-specific cyber threats?

> **Answer:** ATLAS

Question: What type of attack involves cloning an AI model by interacting with its API?

> **Answer:** model theft

Question: What generative AI technique can replicate a person’s voice or appearance with high realism?

> **Answer:** deepfake

Question: What common social engineering attack has become harder to detect due to AI-generated fluent and convincing messages?

> **Answer:** phishing

***

## Task 5: Defensive AI

<figure><img src="../.gitbook/assets/image (186).png" alt="" width="183"><figcaption></figcaption></figure>

Despite the risks, AI is a massive win for defenders. IBM reports that AI adoption saves companies an average of **$2.2 Million** in breach costs.

#### Defensive Capabilities

<figure><img src="../.gitbook/assets/image (187).png" alt="" width="188"><figcaption></figcaption></figure>

* **Analysis:** Identifying anomalies in network traffic at speeds impossible for humans.
* **Prediction:** Automating security workflows to block threats before they reach the user.
* **Investigation:** Using LLMs to summarize complex logs or imagine creative **Threat Hunting** scenarios.

#### Securing AI

<figure><img src="../.gitbook/assets/image (188).png" alt="" width="188"><figcaption></figcaption></figure>

To protect the AI itself, organizations must implement **RBAC/MFA**, encrypt training data, use standards like **ISO/IEC 27090**, and utilize explainability tools like **SHAP** or **LIME** for monitoring.

Question: According to IBM, how many days faster does AI help identify and contain breaches?

> **Answer:** 108

Question: What cybersecurity task benefits from AI helping to imagine attacker behavior we might not consider?

> **Answer:** threat hunting

Question: Explainability tools such as SHAP and LIME help with what?

> **Answer:** model monitoring

***

## Task 6: Practical - AI Assistant

In the practical lab, we used a defensive AI assistant to analyze logs and identify technical configurations. This task demonstrates how AI can help defensive security through generating regex detection patterns, log analysis, and more!

#### Flag Values

* **DNS over HTTPS (DoH) Port:** 443
* **SYN Flood Timeout:** 60
* **Windows Ephemeral Port Range Size:** 16384

Question: What's the flag?

> **Answer:** thm{443/60/16384}

***

## Conclusion

AI is the new front line of cybersecurity. By understanding the different learning algorithms: from Supervised to Reinforcement Learning: we can better secure the ML lifecycle. While attackers use AI to enhance malware and phishing, defenders can leverage the same technology to scale investigations and identify threats significantly faster.
