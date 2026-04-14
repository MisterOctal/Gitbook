---
icon: robot
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1748855517880
coverY: 0
coverHeight: 138
---

# AI Forensics

## icon: search

**Date:** 14.04.2026

**Room Category:** Walkthrough

**Core Concept:** Applying Artificial Intelligence and Machine Learning to Digital Forensics and Incident Response (DFIR) and understanding its legal and ethical challenges.

As cyber-attacks grow in complexity and scale, traditional manual forensic methods are no longer sufficient to keep pace with modern adversaries. Mastering the AI forensics landscape is essential for the modern investigator to harness machine learning as a force multiplier.&#x20;

***

## Task 1: Introduction

Digital Forensics is fundamentally a race against time. Investigators must sift through terabytes of logs, memory dumps, and disk images to find a "needle in a haystack" while an incident may still be ongoing. Traditional DFIR relies on static signatures and manual keyword searches, which fail against sophisticated, living-off-the-land (LotL) attacks.

<figure><img src="../.gitbook/assets/image (199).png" alt="" width="239"><figcaption></figcaption></figure>

AI introduces a paradigm shift by moving from reactive searching to proactive discovery. By leveraging machine learning, we can automate the correlation of disparate data sources, allowing human analysts to focus on high-level decision-making rather than repetitive data normalization.

Question: I'm ready to learn!

> **Answer:** No answer needed

***

## Task 2: The AI Forensics Landscape

<figure><img src="../.gitbook/assets/image (200).png" alt="" width="188"><figcaption></figcaption></figure>

The integration of AI into forensics revolves around its ability to handle "The Four Vs" of big data: Volume, Velocity, Variety, and Veracity.

#### Core Capabilities

* **UEBA (User and Entity Behavior Analytics):** AI establishes a baseline of "normal" behavior for every user and device. When an admin logs in at 3 AM from a new IP, the AI flags the anomaly based on historical patterns rather than a static rule.
* **Scalability:** Machine learning models can process millions of log lines in seconds, a task that would take a human team days to complete.

#### Critical Limitations and Metrics

Because AI is probabilistic, investigators must understand its margin of error.

* **Non-determinism:** Unlike a script that always gives the same result, AI models can produce varying outputs. This poses a challenge for "repeatability" in forensic science.
* **Precision vs. Recall:** In a forensic context, high **Recall** is often prioritized to ensure no evidence is missed, even if it leads to more false positives (lower **Precision**).
* **GIGO (Garbage In, Garbage Out):** A model trained on biased or incomplete data will produce flawed forensic conclusions.

Question: What ability of AI helps turn a DFIR investigator by recognising patterns they might not have been able to comprehend?

> **Answer:** Anomaly Detection

Question: Which metric tells you the proportion of positively flagged results that were actually correct?

> **Answer:** Precision

Question: What term describes the AI characteristic where the same input may yield different outputs across different runs?

> **Answer:** non-determinism

***

## Task 3: AI & DFIR

AI applications in DFIR are specialized based on the type of evidence being analyzed.

<figure><img src="../.gitbook/assets/image (201).png" alt="" width="375"><figcaption></figcaption></figure>

#### Specialized AI Models

* **Convolutional Neural Networks (CNNs):** These are the gold standard for image and video forensics. They can detect "deepfakes," identify manipulated pixels in forged documents, and perform automated license plate or face recognition across thousands of hours of CCTV footage.
* **Natural Language Processing (NLP):** NLP is used to analyze the "style" of a phishing email (Stylometry) to attribute it to a specific threat actor or to perform sentiment analysis on leaked chat logs to identify insider threats.
* **Automated Timeline Reconstruction:** AI can ingest logs from firewalls, endpoints, and cloud providers to automatically build a chronological map of an attack, highlighting the "Pivot Points" where an attacker moved laterally.

Question: What type of neural network is commonly used in image and video forensics?

> **Answer:** Convolutional Neural Network

Question: What kind of analysis can be performed on social media or chat logs to assess emotional tone?

> **Answer:** Sentiment Analysis

Question: What type of data do AI systems correlate to reconstruct the timeline of an incident automatically?

> **Answer:** Time-Sequenced

Question: What type of analysis observes how a program behaves to determine whether it is malicious?

> **Answer:** Dynamic Analysis

***

## Task 4: AI Legal & Ethical Implications

Introducing AI into a courtroom brings unique challenges regarding the "Chain of Custody" and the "Right to Explanation."

<figure><img src="../.gitbook/assets/image (202).png" alt="" width="188"><figcaption></figcaption></figure>

#### Challenges in the Legal System

* **Explainability (XAI):** Most advanced AI models are **Black Boxes**. If an investigator cannot explain _why_ an AI flagged a file as malicious, that evidence may be thrown out.
* **The Daubert Standard:** In the US, for AI evidence to be admissible, the underlying theory must be tested, peer-reviewed, and have a known error rate.
* **Algorithmic Bias:** If an AI used for facial recognition was only trained on specific demographics, it may produce "False Positives" that lead to wrongful accusations.
* **Data Privacy:** Using cloud-based AI to analyze sensitive evidence can violate privacy laws like GDPR. **Federated Learning** is a potential solution, allowing models to learn from data without the data ever leaving the local forensic workstation.

Question: What legal test used in the U.S. assesses whether expert or scientific testimony is admissible?

> **Answer:** Daubert

Question: What term describes AI models whose internal processes are difficult to interpret?

> **Answer:** Black Box

Question: What technology has been shown to produce racially biased results?

> **Answer:** Facial Recognition

Question: What technique allows ML to be performed without transferring sensitive data to a central server?

> **Answer:** Federated Learning

***

## Task 5: Practical - The Digital Trail

<figure><img src="../.gitbook/assets/image (203).png" alt="" width="266"><figcaption></figcaption></figure>

This task puts theory into practice using the Scikit-learn library to analyze a breach at RobbCo. We used a "Hybrid" approach where AI identified the anomalies and human insight confirmed the intent.

#### Detailed Investigation Timeline

1. **Initial Foothold:** The `classify_logs.py` script detected a successful login for `j.morgan` at an unusual time (03:01:02). This led to the discovery of a phishing email and a malicious `.ods` file.
2. **Reconnaissance & Staging:** The attacker's macro dumped system info to `/tmp/invoice_dump.txt`. The AI script `file_anomalies.py` flagged this due to its high entropy and unusual location.
3. **Privilege Escalation:** By reviewing `j.morgan`'s `.bash_history`, we saw the abuse of `sudo` to plant a backdoor SSH key in the `r.house` (founder) directory.
4. **Persistence:** The attacker deployed a reverse shell at `/usr/local/bin/sysmon`. The AI flagged this not because of the filename, but because the binary was attempting outbound connections to an external IP on port 5555.
5. **Data Exfiltration:** The final objective was identified in `/dev/shm/`, a stealthy "in-memory" storage area. The file `.core_dump_2025.tgz.enc` contained the stolen RETROS BIOS source code.

Question: At what time does the attacker successfully log in as j.morgan?

> **Answer:** 03:01:02

Question: What attack method was used to gain initial access?

> **Answer:** Phishing

Question: Can you find the attacker's email address?

> **Answer:** akeane@poseidonenergy.net

Question: What command did the attacker run as j.morgan to gain access to the r.house account?

> **Answer:** sudo nano /home/r.house/.ssh/authorized\_keys

Question: What is the full path of the archive used to steal RobbCo's source code?

> **Answer:** /dev/shm/.core\_dump\_2025.tgz.enc

***

## Conclusion

This case demonstrates that AI is a powerful "force multiplier" but not a replacement for a skilled investigator. While the Scikit-learn scripts saved us hours of manual log review, it was human intuition that connected the phishing email to the lateral movement.

A few things to note from this complex room:

* **Speed vs. Accuracy:** AI gives us the speed to react to breaches in real-time, but human validation ensures accuracy and legal admissibility.
* **Continuous Evolution:** As attackers begin using AI to automate their exploits, forensic teams must adopt AI-driven defense to maintain parity.
* **Ethical Responsibility:** As forensic professionals, we must ensure our use of AI is transparent, explainable, and free from biases that could compromise the integrity of an investigation.

While the "Digital Trail" may be growing longer and more complex, the combination of machine learning and human intellect remains our strongest defense in the search for digital truth.
