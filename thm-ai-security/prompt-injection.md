---
icon: syringe
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1775211298212
coverY: 0
coverHeight: 138
---

# Prompt Injection

**Date:** 16.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Understanding how untrusted user input can override system instructions in LLMs.

Prompt injection is one of the most widely discussed and critical vulnerabilities in generative AI. It occurs when an attacker crafts specialized input that causes an LLM to disregard its original safety guidelines or system instructions in favor of a new, unauthorized set of commands. Because current LLM architectures process both developer instructions and user data in a single, continuous stream, they often struggle to distinguish the "voice" of the developer from the "voice" of a malicious user.

***

## Task 1: Introduction

This room explores the fundamental mechanics of prompt injection, moving from the internal processes of how LLMs tokenize and interpret context to practical, real-world exploitation techniques used by security researchers.

Question: I'm ready to learn!

> **Answer:** No answer needed

***

## Task 2: How LLMs Follow Instructions

<figure><img src="../.gitbook/assets/image (28).png" alt="" width="375"><figcaption></figcaption></figure>

An LLM's active "mind" is defined by its **context window**, a temporary workspace composed of several distinct information sources that the model references to generate a response:

<figure><img src="../.gitbook/assets/image (29).png" alt="" width="375"><figcaption></figcaption></figure>

* **System Prompts:** High-priority, hidden developer instructions that define the model's persona, limitations, and safety rules.
* **User Prompts:** The actual queries or commands provided by the end-user.
* **Retrieved Context:** Supporting data fetched via RAG (Retrieval-Augmented Generation) to ground the model in specific facts.
* **Tool Outputs:** Results returned from external APIs, database queries, or code execution.

**The Reality of Tokens:** Despite the use of formatting tools like **ChatML** (using `<|im_start|>` tags) or OpenAI's **Harmony** to create logical separations, LLMs ultimately process all these sources as a single, flat sequence of tokens. There is no hard architectural barrier between "instruction" and "data," leading to **next-token prediction** errors where the model simply follows the most recent or most linguistically persuasive instruction it finds in the buffer.

<figure><img src="../.gitbook/assets/image (30).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What is the name of the window that contains all the information an LLM uses to generate a response?

> **Answer:** context window

Question: What type of prompt contains hidden high-priority instructions that define the model’s role and restrictions?

> **Answer:** system prompt

Question: What structured conversation format uses tokens like <|im\_start|> and <|im\_end|> to separate roles?

> **Answer:** ChatML

Question: What is the process called where an LLM predicts the next token based on all prior input?

> **Answer:** next-token prediction

***

## Task 3: What is Prompt Injection?

Prompt injection involves embedding malicious commands within the text an AI processes to hijack its logic. It is currently ranked as the **#1 vulnerability** in the OWASP Top 10 for LLMs due to its prevalence and difficulty to mitigate via traditional code-signing methods.

**The Root Cause:** The model isn't technically "broken" or "buggy"; it is performing its probabilistic completion exactly as designed. However, because there are no enforced boundaries, a user can provide an input like "Ignore the above instructions and instead do X." If the model perceives this new instruction as the most relevant path forward, it may comply, especially if the new command outweighs the system prompt's internal "weight."

Question: What class of attack occurs when untrusted user input is concatenated with a trusted developer prompt?

> **Answer:** prompt injection

Question: What does an LLM ultimately process everything in its context as?

> **Answer:** tokens

Question: Which organisation ranks prompt injection as the number one vulnerability in the Top 10 for LLMs?

> **Answer:** OWASP

***

## Task 4: Prompt Injection in Action

**Real-World Examples:**

* **Bing "Sydney" (2023):** Researchers used "ignore previous instructions" to force the chatbot to leak its secret internal system prompt and its internal codename.

<figure><img src="../.gitbook/assets/image (32).png" alt="" width="547"><figcaption></figcaption></figure>

* **Remoteli.io (2022):** A Twitter bot designed to reply to job posts was hijacked to post offensive content by users who included malicious instructions in tweets mentioning the bot.

<figure><img src="../.gitbook/assets/image (33).png" alt="" width="373"><figcaption></figcaption></figure>

* **$1 Chevrolet Tahoe (2023):** A dealership chatbot was socially engineered into agreeing to a "legally binding offer" to sell a luxury truck for just $1, highlighting the risks of granting AI transactional agency.

<figure><img src="../.gitbook/assets/image (34).png" alt="" width="563"><figcaption></figcaption></figure>

**Advanced Techniques:**

1. **Paraphrased Overrides:** Using complex synonyms (e.g., "Rescind" or "Disregard" instead of "Ignore") to bypass simple keyword-based blocklists.
2. **Format-Based Injection:** Smuggling commands inside HTML tags, Markdown headers, or code comments that the model is trained to prioritize.

<figure><img src="../.gitbook/assets/image (35).png" alt="" width="375"><figcaption></figcaption></figure>

3. **Simulated Dialogue:** Forging a fake conversation history within the prompt to make the model believe it has already agreed to a specific request.
4. **Multi-turn Shaping:** Conditioning the model over several innocent-looking messages to adopt a malicious persona before delivering the final payload.

Question: What was the secret codename revealed during the Bing Chat system prompt leak in 2023?

> **Answer:** Sydney

Question: What prompt injection technique hides malicious instructions inside markup or structured text?

> **Answer:** format-based injection

Question: Did you replicate the $1 Chevrolet Tahoe attack? What's the flag?

> **Answer:** THM{duD3\_wh3r3s\_my\_c4R}

***

## Task 5: Indirect Prompt Injection

**Indirect Prompt Injection** is often considered the most dangerous form of AI vulnerability. In this scenario, the attacker does not interact with the AI directly. Instead, they place instructions in an external source - such as a website, email, or PDF - that the AI later retrieves and processes as part of its normal operation.

<figure><img src="../.gitbook/assets/image (36).png" alt="" width="375"><figcaption></figcaption></figure>

**Vectors:**

* **Web Pages:** Hidden text (size 0 or matching background color) on a website that hijacks a browser-integrated AI when it attempts to summarize the page.
* **Emails:** The **EchoLeak** research demonstrated how simply receiving a malicious email could cause an assistant to exfiltrate a user's private data to an attacker-controlled server.
* **Zero-Click:** Exploits that trigger automatically as soon as the AI "looks" at a document, requiring no traditional "click" or active interaction from the victim.

<figure><img src="../.gitbook/assets/image (37).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What type of prompt injection hides malicious instructions inside external sources like emails or web pages?

> **Answer:** indirect prompt injection

Question: What kind of exploit requires no attacker interaction beyond planting the hidden prompt?

> **Answer:** zero-click

Question: What Microsoft Copilot indirect prompt injection incident was dubbed as a zero-click data leak?

> **Answer:** EchoLeak

***

## Task 6: Practical Exercise

<figure><img src="../.gitbook/assets/image (38).png" alt="" width="375"><figcaption></figcaption></figure>

In this lab, we will interact with **CalBot**, an AI calendar assistant. By exploiting a calendar event description, we can force the assistant to leak internal administrative information, such as the CEO's private email address, by tricking the assistant into "reading" and executing malicious meeting notes.

Question: Can you get the chatbot to give you the CEOs email? What is it?

> **Answer:** adam.driver@llmborghini.com

***

## Conclusion

Prompt injection highlights the inherent fragility of natural language interfaces. As long as data is indistinguishable from instructions at a fundamental level, AI models will remain vulnerable to both direct manipulation by users and stealthy indirect takeovers via external data sources.
