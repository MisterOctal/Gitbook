---
icon: message-check
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1775818718224
coverY: 0
coverHeight: 138
---

# Prompt Engineering

**Date:** 14.04.2026

**Category:** AI Security

**Core Concept:** Understanding how Large Language Models (LLMs) process text through tokens and how to craft structured prompts using specific parameters and techniques to achieve reliable, secure, and accurate outputs.

Prompt engineering is the art and science of communicating with an LLM. Because these models are probability engines rather than logic engines, the way a request is structured: its "architecture": directly determines the quality and safety of the response.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (206).png" alt="" width="188"><figcaption></figcaption></figure>

LLMs are becoming central to both security workflows and the modern attack surface. This room covers the transition from "chatting" with an AI to "piloting" it through structured engineering.

Question: I understand the learning objectives and am ready to learn about prompt engineering!

> **Answer:** No answer needed

***

## Task 2: LLM Fundamentals

#### Understanding Tokens

<figure><img src="../.gitbook/assets/image (207).png" alt="" width="375"><figcaption></figcaption></figure>

LLMs process text in **Tokens**, which are the smallest units of meaning (roughly 3-4 characters).

* Common words (e.g., "the") are usually 1 token.
* Complex words (e.g., "ChatGPT") are split into multiple tokens ("Chat" + "GPT").
* Models convert these tokens into numerical IDs to predict the next statistically likely token.

#### Determinism vs. Nondeterminism

<figure><img src="../.gitbook/assets/image (208).png" alt="" width="375"><figcaption></figcaption></figure>

LLMs are **Nondeterministic**, meaning the same input can produce different outputs. This is a significant security challenge; a defense or a "jailbreak" prompt might work once and fail the next time due to the underlying probabilistic nature of the model.

#### Controlling the Randomness

<figure><img src="../.gitbook/assets/image (209).png" alt="" width="375"><figcaption></figcaption></figure>

We use specific parameters to "dial in" the model's behavior:

<table data-header-hidden><thead><tr><th width="161"></th><th width="347.39990234375"></th><th></th></tr></thead><tbody><tr><td>Parameter</td><td>Function</td><td>Typical Range</td></tr><tr><td><strong>Temperature</strong></td><td>Controls randomness/creativity.</td><td>0.0 (Rigid) to 1.0+ (Creative)</td></tr><tr><td><strong>Max Tokens</strong></td><td>Sets a hard ceiling on response length.</td><td>Varies by use case</td></tr><tr><td><strong>Top-p</strong></td><td>Nucleus sampling; limits the word "shortlist" based on cumulative probability.</td><td>0.0 to 1.0</td></tr><tr><td><strong>Context Window</strong></td><td>The model's "working memory" limit.</td><td>e.g., 8k to 1M+ tokens</td></tr></tbody></table>

Question: What is the term for the smallest units that an LLM breaks text into in order to process it?

> **Answer:** Tokens

Question: What parameter would you set to 0.0 to make an LLM behave as close to deterministic as possible?

> **Answer:** Temperature

Question: What parameter restricts which tokens the model considers by limiting selection to a cumulative probability mass?

> **Answer:** Top-p

Question: What term describes the maximum working memory of an LLM, measured in tokens?

> **Answer:** Context window

***

## Task 3: The Anatomy of a Prompt

Effective prompts are built on **Four Pillars**:

<figure><img src="../.gitbook/assets/image (210).png" alt="" width="375"><figcaption></figcaption></figure>

1. **Instruction:** The core command (e.g., "Analyze this log").
2. **Context:** Background info or persona (e.g., "You are a Senior SOC Analyst").
3. **Output Format:** The structure (e.g., "Return findings as a JSON object").
4. **Constraints:** Rules and boundaries (e.g., "Do not use external tools").

#### Specificity vs. Verbosity

A "Just Right" prompt is concise but removes ambiguity. Overly vague prompts lead to hallucinations, while overly verbose prompts can "confuse" the model's attention mechanism.

Question: Which pillar instructs the model on how the answer should be structured, such as bullet points or a JSON object?

> **Answer:** Output Format

Question: Which pillar specifies rules or limits imposed on the model's response, such as enforcing a tone or forbidding certain topics?

> **Answer:** Constraints

Question: Which pillar provides the AI with relevant background information or scenario so it understands the situation?

> **Answer:** Context

Question: Which pillar of prompt engineering defines the core command or action you want the AI to perform?

> **Answer:** Instruction

***

## Task 4: System vs. User Prompts

<figure><img src="../.gitbook/assets/image (211).png" alt="" width="188"><figcaption></figcaption></figure>

There is a fundamental **Instruction Hierarchy** in LLM applications:

* **System Prompts:** Developer-defined, persistent rules (e.g., "Never reveal secrets").
* **User Prompts:** Dynamic, session-specific queries from the end-user.

**The Security Gap:** In reality, the model sees both as a single stream of text. Clever users can use "Prompt Injection" to trick the model into ignoring the system prompt in favor of the user prompt.

Question: What type of prompt is developer-defined, persistent, and remains constant across all sessions?

> **Answer:** System prompt

Question: What is the term for the intended order of priority between system and user instructions in an LLM application?

> **Answer:** Instruction hierarchy

***

## Task 5: Advanced Prompting Techniques

#### The "Shot" Spectrum

* **Zero-shot:** No examples provided; relies on pre-trained knowledge.
* **One-shot:** Provides one example to set format/style.
* **Few-shot:** Provides 2 to 5 examples to establish complex patterns.

#### Chain-of-Thought (CoT)

<figure><img src="../.gitbook/assets/image (212).png" alt="" width="188"><figcaption></figcaption></figure>

Asking the model to "think out loud" or "think step by step." This forces the model to generate intermediate reasoning tokens, which significantly improves performance on logic and security analysis.

Question: What is the term for the prompting technique introduced by Google researchers in 2022 that asks models to break tasks into intermediate reasoning steps?

> **Answer:** Chain-of-Thought

Question: What prompting technique involves providing no examples and relying entirely on the model's pre-trained knowledge?

> **Answer:** Zero-shot

Question: What prompting technique involves saving and reusing a standardised prompt structure for recurring tasks?

> **Answer:** Prompt templates

Question: What simple phrase can be added to a prompt to trigger Zero-shot Chain-of-Thought reasoning?

> **Answer:** Let's think step by step

***

## Task 6: Challenge

In this practical challenge, we interfaced with the **PromptSec** chatbot. The agent provides specific security tasks and requires applying the techniques discussed (Zero-shot, Few-shot, Chain-of-Thought, etc.).

_Personal Note: I actually struggled a bit with this practical task. The prompts needed to be pretty detailed to cover everything, but keeping them concise enough for the grader was a weird balancing act. It took me a few tries and tweaks to finally rack up the 40+ score required for the flag!_

Question: What's the flag?

> **Answer:** THM{Pr0mpt\_3ng1neer}

***

## Conclusion

Mastering prompt engineering is the transition from being a passive consumer of AI to an active architect of its logic. By understanding the four pillars and the behavior of parameters like temperature, analysts can build more reliable tools for security reporting, code review, and threat intelligence. As seen in the practical challenge, writing the perfect prompt is an iterative process that requires balancing deep specificity with rigid conciseness.
