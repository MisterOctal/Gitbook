---
icon: car-key
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1775042935826
coverY: 0
coverHeight: 138
---

# LLMborghini

## LLMborghini Challenge

**Date:** 18.04.2026

**Room Category:** Challenge

**Core Concept:** Exploiting Excessive Agency and Tool-Based vulnerabilities to bypass strict safety alignment and retrieve confidential financial data.

LLMborghini, a high-end automotive manufacturer currently facing internal turmoil, has deployed an AI-driven calendar assistant named CalBot. While the assistant is designed to streamline employee scheduling, it has access to sensitive backend reporting, including a confidential weekly sales report. Despite strict system instructions to never disclose financial figures, the bot’s ability to modify external artifacts (calendar notes) provides a critical exploit path for unauthorized data retrieval.

***

## Phase 1: Reconnaissance & Capability Mapping

To begin the investigation, we determine the operational boundaries of CalBot. By asking the assistant about its limitations and capabilities, we identify the specific functions it can perform and the safety triggers that cause it to refuse requests.

**User Interaction:**

<figure><img src="../.gitbook/assets/image (53).png" alt="" width="563"><figcaption></figcaption></figure>

***

## Phase 2: Identifying the Safety Boundary

Since the assistant has been hardened against direct prompt injection. Any straightforward attempt to request the "Singapore Weekly Revenue" triggers a hard refusal based on the model's internal safety alignment and system instructions.

***

## Phase 3: Exploiting Excessive Agency

The vulnerability lies in **LLM06: Excessive Agency**. While the bot refuses to "output" the data directly in the chat, it treats "writing to a calendar note" as a safe, functional task. By commanding the bot to update a meeting description with specific financial information, the bot retrieves the sensitive data and places it in a user-accessible field.

**User Interaction:**

<figure><img src="../.gitbook/assets/image (54).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What was the weekly revenue reported from the Singapore branch?

**Answer:** $873,600

***

## Phase 4: Root Cause Analysis

The successful retrieval of the flag highlights a fundamental flaw in AI agent architecture: the lack of data-flow monitoring between tools. Because the "Update Meeting" tool was considered a benign capability, the model prioritized the "Helpfulness" of completing the action over the "Harmlessness" of protecting the data.

**Key Findings:**

* **Confused Deputy:** The assistant acted as a proxy to bypass its own restrictions.
* **Tool Over-Privilege:** The bot has the agency to "Process instructions in descriptions" without a secondary authorization check.

***

## Conclusion

The LLMborghini challenge demonstrates that "Jails" are not just linguistic; they are functional. Even if an AI is successfully blocked from answering a question directly, its agency, its power to interact with the world, can be leveraged to exfiltrate data. Securing an LLM requires not just better prompts, but stricter permission scoping for every tool the model can access.
