---
icon: link-horizontal
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/69650d18bb3fe8c456972924-1770852971316
coverY: 0
coverHeight: 138
---

# AI Supply Chain Security

**Date:** 19.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Analyzing how dependencies on external models, datasets, and frameworks create a "Chain of Trust" that attackers can exploit.

Modern AI development relies heavily on pre-trained components. Every time a developer runs `model.load()` or `pip install`, they are trusting a long chain of unverified actors, infrastructure, and data. This room explores the vulnerabilities inherent in this supply chain, from malicious pickle files to compromised build pipelines.

***

## Task 1: Introduction

AI supply chain attacks are effective because they exploit trust rather than bypassing traditional perimeters. In 2024, researchers found over 100 models on Hugging Face that looked legitimate but opened reverse shells upon being loaded. This room maps the landscape of these upstream threats.

Question: I'm ready to learn about AI supply chains!

> **Answer:** No answer needed

***

## Task 2: What Is a Supply Chain?

A supply chain is the network of all individuals, organizations, resources, activities, and technology involved in the creation and sale of a product.

<figure><img src="../.gitbook/assets/image (248).png" alt=""><figcaption></figcaption></figure>

**Key Concepts:**

* **Transitive Dependencies:** Packages pulled in automatically by your direct dependencies. You are responsible for every link in this chain.
* **SolarWinds (2020):** Attackers compromised the build process, causing customers to install a backdoored version of a trusted update.
* **Log4Shell (2021):** A vulnerability in a widely used library (Log4j) exposed millions of applications that relied on it as an upstream component.

Question: In the SolarWinds attack, where in the supply chain was the malicious code injected?

> **Answer:** Build Process

Question: While installing torch Pip also pulls in filelock, which you never listed. What type of dependency is filelock?

> **Answer:** Transitive Dependency

***

## Task 3: The AI Supply Chain

AI supply chains add a new artifact: **Trained Models**. These are more than just code; they represent serialized weights that capture everything a model "learned" during training.

<figure><img src="../.gitbook/assets/image (250).png" alt=""><figcaption></figcaption></figure>

**The Four Components:**

1. **Models:** Pre-trained weights and neural architectures (for example, BERT, GPT-2).
2. **Datasets:** The information used to train and evaluate models (for example, ImageNet).
3. **Frameworks:** Libraries used to build and run AI (for example, PyTorch, TensorFlow).
4. **Dependencies:** Supporting packages required by frameworks (for example, NumPy).

**Transfer Learning Risk:** Most teams fine-tune existing models. If a base model is poisoned during pre-training, that backdoor can survive the fine-tuning process and remain active in the final application.

<figure><img src="../.gitbook/assets/image (249).png" alt=""><figcaption></figcaption></figure>

Question: What are the four key components of an AI supply chain? (listed alphabetically)

> **Answer:** datasets, dependencies, frameworks, models

Question: What do model files contain that allows them to run code when loaded?

> **Answer:** Serialised Objects

***

## Task 4: Two Ways to Consume AI

Organizations typically interact with AI through two distinct paradigms, each with a different risks.

<figure><img src="../.gitbook/assets/image (251).png" alt=""><figcaption></figcaption></figure>

**Paradigm 1: Downloading Model Files**

* **Risk:** High. You run the model on your infrastructure.
* **Formats:** \* `.pkl`, `.pt`, `.bin`: Use Python's pickle serialization, which is highly vulnerable to remote code execution (RCE).
  * `.safetensors`: Eliminates serialization-level risk by storing raw weights only.
  * `.gguf`: Dominant format for local LLMs (for example, Llama), typically quantized for consumer hardware.

**Paradigm 2: Calling Models via API**

* **Risk:** Lower RCE risk, but higher "Black Box" risk.
* **Supply Chain:** You are trusting the provider's training data, fine-tuning, and hosting security. The supply chain is invisible and controlled entirely by the provider.

Question: What is the dominant file format for running local large language models such as LLaMA, Mistral, and Qwen?

> **Answer:** gguf

***

## Task 5: The Four Attack Layers

Attackers target specific "checkpoints" in the supply chain to gain maximum leverage.

<figure><img src="../.gitbook/assets/image (252).png" alt=""><figcaption></figcaption></figure>

1. **Model Layer:**
   * **Serialization-level:** Hiding code in the file format (Pickle attacks).
   * **Architecture-level:** Malicious logic in the neural layers.
   * **Weights-level:** Altering learned values to trigger on specific inputs.
2. **Dependency Layer:** Typosquatting and dependency confusion in package managers like PyPI.
3. **Data Layer:** Poisoning as little as 0.1% of a dataset to introduce a reliable backdoor.
4. **Infrastructure Layer:** Stealing maintainer credentials or compromising build pipelines (CI/CD).

Question: At which layer of the AI supply chain do pickle-based attacks occur?

> **Answer:** Model Layer

Question: Which level of model attack is eliminated by converting to SafeTensors format?

> **Answer:** Serialisation-level

Question: Researchers find that 0.1% of a public training dataset has been replaced with crafted samples designed to introduce a backdoor. Which attack layer does this represent?

> **Answer:** Data Layer

***

## Task 6: Real-World Incidents

Supply chain attacks are common and high-impact.

<figure><img src="../.gitbook/assets/image (253).png" alt=""><figcaption></figcaption></figure>

* **PyTorch (2022):** "torchtriton" dependency confusion stole SSH keys and Git configs.
* **Hugging Face (2023-2024):** Exposure of API tokens and malicious pickle models.
* **Ultralytics (2024):** A cryptominer was embedded into the YOLO library via a GitHub Actions compromise.
* **NullifAI (2025):** Malformed pickle files were used to bypass automated security scanners on Hugging Face.

Question: The torchtriton package exploited pip's version resolution to install a public package over an internal one. Which of the four attack layers does this target?

> **Answer:** Dependency Layer

Question: The @solana/web3.js attacker stole a maintainer's credentials to push malicious updates to a legitimate, high-trust repository. Which attack layer does this represent?

> **Answer:** Infrastructure Layer

***

## Task 7: Practical Evaluation

When reviewing models on repositories like Hugging Face, several signals indicate trustworthiness:

* **Verified Organization:** Established accounts with a track record.
* **Download Count:** High volume suggests community vetting.
* **File Format:** Preference for SafeTensors over Pickle.
* **Security Scans:** Checking for automated "malware detected" flags.

Below is an image of a suspicious model on Hugging Face:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-19 091343.png" alt=""><figcaption></figcaption></figure>

Below is an image of a verified model on Hugging Face:

<figure><img src="../.gitbook/assets/Screenshot 2026-04-19 091349.png" alt=""><figcaption></figcaption></figure>

Question: In the static site, what is the name of the unverified organisation that uploaded the model?

> **Answer:** trustworthy-ai-models

Question: How many downloads does this model have (last month)?

> **Answer:** 127

Question: What file format does the verified model (google-bert/bert-base-uncased) use for its weights?

> **Answer:** SafeTensors

***

## Conclusion

Trust is the ultimate vulnerability in a supply chain. SafeTensors and automated scanners are important tools, but they are not silver bullets. A comprehensive defense requires understanding that model files are "code in disguise" and that every upstream dependency, no matter how small can compromise the entire system.
