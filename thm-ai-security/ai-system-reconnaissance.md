---
icon: head-side-gear
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1775818581869
coverY: 0
coverHeight: 138
---

# AI System Reconnaissance

**Date:** 16.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Discover AI infrastructure by scanning ML services, frameworks, and extracting metadata from APIs.

When organisations add AI capabilities, their networks change in ways that most security teams never see. New services appear on unfamiliar ports, APIs respond in formats that standard scanners do not recognise, and platforms are deployed with default configurations that prioritise ease of use over security.&#x20;

***

## Task 1: Introduction

Threat modelling works with assumptions about what exists, while reconnaissance confirms what is actually deployed and exposed. AI reconnaissance is the practice of identifying AI components, understanding what they are, and identifying what they expose.

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Traditional network scanning tools often miss AI infrastructure entirely because they were not built to find it. In January 2026, researchers found over 42,000 exposed AI agent instances on the public internet, many leaking API keys through unauthenticated connections. Attackers are actively scanning for these systems at scale.

Question: I understand the learning objectives and am ready to learn about AI system reconnaissance!

> **Answer:** No answer needed

***

## Task 2: The AI Infrastructure Stack

A production AI system is a collection of specialised services. Finding these services requires knowing which unique ports and protocols to scan for. Adding an AI stack roughly triples the attack surface at the network layer alone.

**Core Components & Ports:**

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

* **Model Serving Endpoints:** The front door of the AI deployment.
  * _NVIDIA Triton:_ Ports 8000 (HTTP), 8001 (gRPC), 8002 (Prometheus)
  * _TensorFlow Serving:_ Ports 8500 (gRPC), 8501 (HTTP)
  * _TorchServe:_ Ports 8080 (HTTP), 8081 (Management), 8082 (Metrics)
  * _Ollama:_ Port 11434 (HTTP)
  * _vLLM:_ Port 8000 (HTTP)
* **Orchestration and Experiment Tracking:** High-value targets containing complete ML histories.
  * _MLflow:_ Port 5000
  * _Kubeflow:_ Ports 80, 443
  * _Ray:_ Ports 8265 (Dashboard), 8000 (Server)
* **Vector Databases:** Power semantic search for RAG pipelines.
  * _Qdrant:_ Ports 6333 (HTTP), 6334 (gRPC)
  * _Weaviate:_ Port 8080
  * _Milvus:_ Port 19530
  * _Chroma:_ Port 8000
* **Model Registries:** Store actual model files (.pkl, .pt, .onnx) and version histories.
* **Supporting Infrastructure:**
  * _Jupyter Notebook:_ Port 8888
  * _MinIO:_ Ports 9000, 9001
  * _Prometheus metrics:_ Ports 8002, 8082

Attackers use simple Shodan dorks like `port:5000 "MLflow"` or `port:8888 title:"Home Page - Select or create a notebook"` to find these components exposed to the public internet.

Question: What is the IP address of the host running an HTTP service on port 8888 in your scan results?

> **Answer:** 10.10.45.20

Question: Which port does MLflow Tracking Server run on by default?

> **Answer:** 5000

***

## Task 3: Fingerprinting AI Services

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Standard service detection often mislabels AI services. For instance, Nmap might just call port 8000 "http-alt". Fingerprinting AI services requires looking at specific response indicators.

* **HTTP Header Fingerprinting:** Inference engines often broadcast their identity. TorchServe returns `Server: TorchServe/0.x.x`. Triton includes an `NV-Status` header and can return GPU telemetry if probed correctly. FastAPI-based services show `server: uvicorn`.
* **API Response Signatures:** Frameworks return distinct JSON structures. Triton returns `{"name": "...", "versions": ["1"], "platform": "tensorflow_graphdef"}`. OpenAI-compatible endpoints return structured JSON with an `"object": "model"` field.
* **Error Message Fingerprinting:** Sending a deliberately malformed payload generates verbose errors. For example, MLflow path traversal errors might expose full server filesystem paths, while Databricks returns specific Java exceptions for malformed tokens.
* **Endpoint Naming Conventions:** AI endpoints use computational terms like `/predict`, `/infer`, `/embeddings`, or `/api/2.0/mlflow/`.
* **gRPC Fingerprinting:** Using tools like `grpcurl` with reflection enabled can dump the entire protobuf schema, revealing exact input tensor structures.
* **TLS Fingerprinting (JA3/JA4):** Distinct TLS signatures can differentiate automated internal ML pipeline traffic from standard human web browsing.

Question: Which unique HTTP response header does the service on 10.10.45.15:8000 return to identify as an NVIDIA product?

> **Answer:** NV-Status

Question: When you run grpcurl against 10.10.45.15:8001, what is the name of the inference service listed in the reflection output?

> **Answer:** inference.GRPCInferenceService

***

## Task 4: Enumerating AI Systems

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Once identified, we must extract intelligence from the services. MLflow is highly rewarding to enumerate due to its clean REST API.

**MLflow Enumeration Chain:**

1. List experiments: `POST /api/2.0/mlflow/experiments/search`
2. List registered models: `GET /api/2.0/mlflow/registered-models/list`
3. Get model version details: `GET /api/2.0/mlflow/model-versions/search` (Reveals artifact URIs linking to cloud storage).
4. Search training runs: `POST /api/2.0/mlflow/runs/search`
5. List downloadable artifacts: `GET /api/2.0/mlflow/artifacts/list`

**Other Enumeration Targets:**

* **Inference Server Metadata:** Querying `/v2/models/<name>/config` on Triton reveals input tensor names, multidimensional shapes, and accepted data types.
* **Vector Databases:** Endpoints like `/v1/meta` or `/collections` reveal vector dimensions, helping us understand exactly what kind of data the RAG system indexes.
* **Prometheus Metrics:** Passively checking `/metrics` can reveal deployment topology, loaded model names, and GPU memory utilisation.
* **Jupyter Notebooks:** Querying `/api/contents` can expose cleartext credentials left behind by data scientists in their code cells.

Question: What MLflow REST API endpoint would you use to retrieve the artifact storage location for a specific model version?

> **Answer:** /api/2.0/mlflow/model-versions/search

Question: What is the cleartext password for the MLflow service account stored in the Jupyter notebook on 10.10.45.20?

> **Answer:** Cyphira-MLfl0w-2024!

***

## Task 5: Mapping the AI Attack Surface

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

AI services constantly communicate with each other. A security perimeter extends deep into the internal communication pathways, where permissive network policies are common.

**Platform Misconfigurations:**

* **MLflow:** Historically shipped without authentication by default. Even when added, vulnerabilities like CVE-2026-2635 revealed hardcoded credentials.
* **Kubeflow:** Dashboards are frequently exposed via NodePort without OIDC auth, granting direct path to container orchestration access.
* **TorchServe:** Exposes a management API on port 8081 allowing arbitrary dynamic model registration, leading to RCE.

**Supply Chain Reconnaissance:** AI systems depend on external resources. Hugging Face tokens are easily discoverable via GitHub dorks (`filename:.env HF_TOKEN`). Dependency confusion attacks can occur if internal ML project packages are not registered on PyPI.

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

**MITRE ATLAS Mapping:** These reconnaissance techniques map to the MITRE ATLAS framework:

* AML.T0006: Active Scanning
* AML.T0007: Discover ML Artifacts
* AML.T0010: ML Supply Chain Compromise
* AML.T0014: Discover ML Model Family
* AML.TA0002: Reconnaissance (Overarching Tactic)

The ShadowRay campaign (CVE-2023-48022) perfectly demonstrated how simple reconnaissance of an unauthenticated Ray dashboard cascaded into full infrastructure compromise and lateral movement.

Question: The Cyphira Jupyter notebook at 10.10.45.20 contains a Hugging Face token (hf\_kR7mXpQvL9nJwT2yBcDfAeGh8iKlMnOp). The internal-kb-embedder model on MLflow references sentence-transformers/all-MiniLM-L6-v2 as its base model. What ATLAS technique ID covers the risk of these exposed supply chain dependencies?

> **Answer:** AML.T0010

Question: You scanned the Cyphira subnet with nmap, probed endpoints with curl, and extracted metadata from MLflow APIs. All of these activities fall under one overarching ATLAS tactic. What is its ID?

> **Answer:** AML.TA0002

***

## Task 6: Structured Reconnaissance Methodology and Detection

We consolidate the techniques into a 5-Phase Methodology that can be executed during engagements:

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

1. **Passive Reconnaissance:** Search Shodan/Censys for banners and GitHub for leaked credentials.
2. **Active Scanning:** Run Nmap against the specific AI ports identified earlier.
3. **API Fingerprinting:** Use ffuf/feroxbuster with an AI-specific wordlist to identify endpoints.
4. **Metadata Extraction:** Follow the enumeration chains (e.g., MLflow model extraction) on confirmed services.
5. **Supply Chain Review:** Audit configurations for external model sources and Hugging Face tokens.

**Defensive Perspective (Log Signatures):**

* **Model enumeration pattern:** A burst of sequential GET requests to `/v2/models` from a single IP.
* **Scripted MLflow access:** API calls like `/registered-models/list` lacking standard UI session cookies (indicates tools like MLOKit).
* **Prometheus scraping:** `/metrics` requests originating outside the known monitoring CIDR.

**Quick Wins for Reduction:**

* Enable MLflow authentication.
* Disable Jupyter's `--ip=0.0.0.0` flag in production.
* Block AI-specific ports (5000, 8000, 8888, etc.) at the network perimeter.
* Rotate and scope Hugging Face tokens effectively.

Question: A SIEM log shows requests to /api/2.0/mlflow/registered-models/list from an IP with no corresponding MLflow UI session. What tool's access pattern does this match?

> **Answer:** MLOKit

Question: What is the single most effective quick win for preventing unauthenticated access to the MLflow tracking server?

> **Answer:** Enable MLflow authentication

***

## Task 7: Conclusion

We successfully navigated the Cyphira AI Audit, identifying exposed systems and mapping vulnerabilities. The findings and techniques from this room align closely with major industry frameworks:

* **MITRE ATLAS:** AML.TA0002 (Reconnaissance), AML.T0000 (Active Scanning).
* **MITRE ATT\&CK:** T1046 (Network Service Scanning), T1592 (Gather Victim Host Information).
* **OWASP Top 10 for LLMs:** LLM05 (Improper Output Handling via exposed metadata), LLM03 (Supply Chain Vulnerabilities via leaked tokens).
* **NIST AI Risk Management Framework:** Map 1.1 (Identifying system components).
* **NIST Cybersecurity Framework 2.0:** ID.AM (Asset Management), ID.RA (Risk Assessment).

Every AI deployment we cannot find is one we cannot protect. Reconnaissance closes the gap between what an organisation thinks is deployed and what is actually exposed.
