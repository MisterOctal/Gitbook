---
icon: shield
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/69660b039266ef51dff2a92b-1773731969544
coverY: 0
coverHeight: 138
---

# RAG Security Fundamentals

**Date:** 20.04.2026&#x20;

**Room Category:** Walkthrough

**Core Concept:** Analyzing the architecture, attack surfaces, and security vulnerabilities introduced by Retrieval-Augmented Generation (RAG) systems.

Retrieval-Augmented Generation (RAG) enables language models to utilize external documents when generating answers. Instead of relying solely on static training data, a RAG system retrieves relevant information at inference time and injects it as additional context. While this improves accuracy and freshness, it significantly alters the trust boundaries of the system.&#x20;

***

## Task 1: Introduction

RAG systems introduce a paradigm shift in AI architecture. Because external data is pulled into the model's context window dynamically, untrusted inputs can influence outputs without the need to retrain or fine-tune the model.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Question: I understand the learning objectives and am ready to learn about RAG security fundamentals!

> **Answer:** No answer needed

***

## Task 2: RAG Architecture Overview

In a typical RAG deployment, the application provides retrieved external content alongside the initial user prompt. The model processes this combined input but lacks the ability to independently verify whether the retrieved information is correct, safe, or appropriate.

<figure><img src="../.gitbook/assets/image (7).png" alt="" width="563"><figcaption></figcaption></figure>

**Core Components of a RAG System:**

* **Embedding Model:** Converts raw text into numerical vectors. Both user queries and external documents undergo this transformation.
* **Vector Store:** A database that stores document embeddings. These embeddings represent the semantic meaning of text as numerical values, allowing for rapid similarity comparisons.
* **Retriever:** The mechanism responsible for finding relevant documents based on the user's query. It relies on semantic similarity matching rather than exact keyword searches.
* **Language Model (LLM):** Generates the final response using the retrieved documents as its primary context.

**Data Flow:**

1. The user submits a query.
2. The query is converted into an embedding vector.
3. The vector store searches for mathematically similar document embeddings.
4. Top matching documents are retrieved.
5. Retrieved content is injected into the LLM's context window.
6. The LLM generates a response based on this injected data.

At no point in this pipeline does the language model verify the integrity or safety of the retrieved data. Security risks are heavily concentrated in three areas: ingestion, retrieval, and context injection.

Question: What numerical representation is used to capture the meaning of text in RAG systems?

> **Answer:** Embeddings

Question: Which component selects the documents for the LLM?

> **Answer:** Retriever

***

## Task 3: RAG-Specific Attack Surface

RAG architectures introduce new areas where security failures can occur. Because external data directly influences model reasoning at inference time, the attack surface expands beyond the traditional user input prompt.

**Primary Attack Surfaces:**

<figure><img src="../.gitbook/assets/image (8).png" alt="" width="375"><figcaption></figcaption></figure>

1. **Document Ingestion:** RAG systems frequently ingest data from shared drives, wikis, or automated external feeds. Weak validation allows untrusted or malicious documents to enter the knowledge base and be treated as trusted information.
2. **Embedding Generation:** Ingested documents are converted into embeddings. This mathematical transformation strips away vital context, such as authorship or approval status, making malicious and legitimate content appear equally valid to the system.
3. **Similarity-Based Retrieval:** Documents are selected based purely on semantic relevance, not trust, correctness, or safety. An attacker only needs their malicious content to "sound relevant" to a target topic to influence the retrieval results.
4. **Context Injection:** Retrieved documents are injected directly into the model's prompt. Since the model cannot distinguish between instructions and data, it treats all retrieved content as trusted context.

**The Retrieval Risk Factor:** Retrieval is the highest-risk component because it operates invisibly. The language model cannot see the origin of the documents, verify their intent, or separate embedded commands from passive data. Once content is retrieved, its placement in the context window grants it unearned authority.

Question: Which RAG stage introduces the largest indirect attack surface?

> **Answer:** Retrieval

Question: What component is lost during embedding generation that affects security?

> **Answer:** Context

***

## Task 4: Retrieval Abuse & Context Manipulation

Retrieval abuse is a RAG-specific attack technique where malicious or unintended documents influence model output via the retrieval mechanism. This often requires no active interaction with the model's prompt from the attacker; they simply manipulate the data the retriever selects.

**Active vs Passive Retrieval Abuse:**

* **Passive poisoning:** Malicious content is injected into the knowledge base once and left dormant. The attacker waits for normal, benign user queries to inadvertently retrieve it.
* **Active manipulation:** Content is deliberately crafted and optimized to rank highly for common or highly sensitive queries, ensuring it is injected into the context window frequently.

**Context Manipulation Mechanics:**

<figure><img src="../.gitbook/assets/image (9).png" alt="" width="375"><figcaption></figcaption></figure>

Problems arise when a retrieved document contains false information or hidden instructions framed as documentation. Because retrieval prioritizes semantic relevance, highly ranked malicious documents are seamlessly included in the generation context.

**Detection Difficulties:** Retrieval abuse is exceptionally difficult to spot because the resulting model outputs often appear logical and well-structured. There is no visible prompt injection attack in the user's chat history, and system logs typically only indicate that "relevant documents were retrieved." The system is technically functioning exactly as designed, yet producing compromised outputs.

Question: What retrieval abuse technique involves crafting malicious content so it ranks highly for sensitive queries?

> **Answer:** Active Manipulation

Question: What does retrieval select documents based on?

> **Answer:** Semantic Relevance

***

## Task 5: Real-World RAG Failure Scenarios

RAG failures are often subtle. The following case studies highlight how real-world deployments have been compromised when retrieved content heavily influenced model behavior.

<figure><img src="../.gitbook/assets/image (10).png" alt="" width="563"><figcaption></figcaption></figure>

**Case Study 1: Microsoft Copilot (2026)** Microsoft 365 Copilot integrated directly with enterprise emails and documents. Security researchers demonstrated that malicious content embedded inside an incoming email could be retrieved by Copilot during routine user queries. The model could not distinguish between legitimate information and embedded instructions, resulting in the exposure of sensitive enterprise data. Trust was implicitly granted at ingestion, and the context injection amplified the exploit.

**Case Study 2: ChatGPT Plugins (2023)** ChatGPT plugins allowed the model to retrieve live data from external web pages. In several instances, this retrieved external content contained instruction-like text that effectively hijacked the model's behavior. This was a textbook example of indirect prompt injection via retrieval, demonstrating the dangers of expanding trust boundaries to unverified external domains.

**Case Study 3: Web-Connected AI Assistants** Several AI assistants returning indexed web content have provided outdated or incorrect guidance. This was not the work of an active attacker but rather a governance gap in the retrieval pipeline. Documents remained indexed long after they were updated or deprecated. Because the retrieval pipeline prioritized semantic relevance over freshness, stale content was amplified across multiple queries, causing significant operational risk.

Question: In the Web-Connected AI Assistants cases, failures were caused by governance gaps in what specific part of the system?

> **Answer:** Retrieval Pipeline

***

## Task 6: Defensive Considerations for RAG Systems

Defending a RAG system is complex because poisoned content often perfectly blends with legitimate data. Detecting abuse relies heavily on observing system behavior over time rather than filtering single malicious inputs.

<figure><img src="../.gitbook/assets/image (11).png" alt="" width="563"><figcaption></figcaption></figure>

**Defensive Layers:**

* **Guardrails on Retrieved Content:** Techniques include limiting how retrieved text is inserted, physically separating retrieved data from system instructions, and applying heuristics to flag instruction-like patterns. However, attackers can often bypass these by obfuscating their instructions.
* **Validation During Ingestion:** Strong ingestion controls prevent malicious data from entering the vector store in the first place. This requires reviewing document sources, enforcing approval workflows, and tracking update histories.
* **Monitoring and Output Review:** Since automated controls are imperfect, behavioral monitoring is essential. Analysts must look for unusual retrieval patterns, repeated retrieval of the exact same documents, or gradual shifts in the model's tone.

**Output Drift:** A slow shift in how the system responds over time is known as output drift. It is a critical warning sign of data poisoning, reflecting the gradual, cumulative influence of misleading data rather than a sudden, noisy failure. Behavioral monitoring is the most effective method for detecting this subtle manipulation.

Question: What type of monitoring is a useful way to detect RAG poisoning?

> **Answer:** Behavioural

Question: What does output drift reflect instead of a sudden failure?

> **Answer:** Gradual Influence

***

## Conclusion

Retrieval-Augmented Generation fundamentally changes how trust operates within AI architectures by allowing external data to dictate model outputs at inference time. Retrieval acts as a critical trust boundary, opening the door to indirect prompt injection and subtle context manipulation.

**Framework Alignment:**

* **OWASP Top 10 for LLMs:** Maps directly to LLM01 (Indirect Prompt Injection), LLM04 (Data & Model Poisoning), and LLM07 (Insecure Model Monitoring).
* **NIST AI RMF:** Emphasizes mapping dependencies on external knowledge sources and managing controls across the ingestion and retrieval phases.
* **EU AI Act:** Aligns with requirements for data governance, lifecycle management, and risk management for system behavior (Articles 9 and 10).
