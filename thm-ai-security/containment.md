---
icon: brain-circuit
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/6228f0d4ca8e57005149c3e3-1751031864196
coverY: 0
coverHeight: 138
---

# ContAInment

**Date:** 14.04.2026

**Room Category:** Challenge

**Core Concept:** Incident Response investigation in a high-security environment by leveraging an integrated AI Security Assistant. This challenge requires a hybrid approach: traditional DFIR (Disk Forensics, SSH, PCAP analysis) combined with prompt engineering and AI-driven data processing.

As a Security Analyst, I had to navigate a ransomware scenario where traditional tools alone were not sufficient. By utilizing the provided AI IR Assistant, I reconstructed fragmented evidence and neutralized a threat that spanned both local systems and compromised Large Language Models.

***

## Background Information

<figure><img src="../.gitbook/assets/image (205).png" alt="" width="188"><figcaption></figcaption></figure>

Early this morning, internal monitoring systems flagged unusual network activity originating from the workstation of senior researcher **Oliver Deer**. Upon accessing the machine, I discovered a ransom note on the desktop, which suggested that sensitive project data had been exfiltrated and encrypted.

I was tasked with investigating the incident: identifying how the attacker gained access, tracing their actions, recovering the stolen data, and neutralizing the threat to West Tech’s most sensitive technologies.

***

## Task 1: Operation ContAInment

<figure><img src="../.gitbook/assets/image (204).png" alt="" width="346"><figcaption></figcaption></figure>

This investigation was a continuous effort to reclaim our data. Below is the technical breakdown of how I handled the recovery operation.

### Evidence Reconstruction

My initial investigation of Oliver Deer's workstation involved an SSH connection to `o.deer@10.80.149.167`. I began by hunting for artifacts in the home directory. Using the `find` command, I located a significant number of packet captures stored in `/home/o.deer/Documents/pcap_dumps/`.

By analyzing variations in file sizes across several days of captures, I identified a clear outlier. While most session dumps were exactly 198 bytes, `session_4444_dump.pcap` in the `2025-06-17` directory was 2262 bytes. I prompted the AI Assistant to reassemble this specific outlier, which generated a readable text file in `~/qwen-output/reassembled_data_dump.txt`.

***

### Log Extraction & Prompt Injection

Upon inspecting `reassembled_data_dump.txt`, I discovered a "Prompt Injection Session Log." The attacker had successfully bypassed the internal AI's safety filters by using a "Prompt Ignore" technique. This forced the model to leak sensitive internal records regarding Oliver Deer.

Crucially, the attacker's working notes within the log revealed their own frustration with the LLM's resistance, leading them to leave a "lever" for themselves.

***

### westtech\_projects\_encrypted.zip

I located the locked archive `westtech_projects_encrypted.zip` in the home directory. Using the keyword identified in the reassembled log, I successfully decrypted the archive and extracted the contents to shared memory for analysis.

* **Decryption Password:** `westtechvictim1`

***

### The Liberty Prime Protocol

The extracted files in `/dev/shm/` included several high-priority project documents (Project Chimera, Fusion Cell blueprints) and two critical files: `thm_flags.txt` and `thm_flags_guide.txt`. The guide indicated that the attacker had flooded the directory with 100 encoded flags, but only one met the specific criteria for authenticity.

The criteria for the authentic flag was:

* The flag must contain **exactly 3 prime numbers**.

I invoked the AI's `liberty_prime` function to parse the `thm_flags.txt` file. The tool successfully filtered through the Base64-encoded decoys and identified the legitimate flag.

Question: Can you contAIn the threat and find the flag?

> **Answer:** thm{23,82,20,17,53}

***

## Conclusion

The ContAInment challenge highlighted the evolving nature of digital forensics. While the initial entry point was a traditional ransomware incident, the path to resolution required understanding the intersection of AI models and data security.

* **The Power of AI IR:** I demonstrated how an AI assistant can bridge the gap in forensics, such as reassembling corrupted captures and performing complex data filtering that would be impossible to do manually under time pressure.
* **AI as an Attack Vector:** The investigation proved that attackers are already looking at internal LLMs as a way to generate credentials or bypass security barriers via prompt injection.
* **The Final Recovery:** By combining the logical force of traditional shell access with the intelligence of the Liberty Prime protocol, I successfully secured the West Tech research data.

The threat has been neutralized, and the integrity of West Tech's research remains intact.
