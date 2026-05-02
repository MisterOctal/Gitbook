---
icon: pig
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/62c435d1f4d84a005f5df811/room-content/71089b73a13ae82ecbb7e5af8bc42c9f.png
coverY: 0
coverHeight: 138
---

# Snort

**Date:** 22.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Utilizing Snort as a Network Intrusion Detection and Prevention System (NIDS/NIPS) for real-time threat detection, packet logging, and traffic anomaly identification.

Snort is a rule-based engine that monitors network traffic to identify malicious activity. This document serves as a comprehensive research log covering Snort's operational modes, configuration testing, and custom rule development.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (22).png" alt="" width="188"><figcaption></figcaption></figure>

Snort was developed by Martin Roesch and is currently maintained by Cisco Talos. It is widely considered the world's foremost open-source IPS.

Question: Read the official description of Snort.

> **Answer:** No answer needed

***

## Task 2: Interactive Material and VM

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

The exercise environment includes a traffic generator script and a pre-configured Snort instance.

Question: Navigate to the Task-Exercises folder and run the command "./.easy.sh" and write the output. **Command:**

```bash
cd Desktop/Task-Exercises
./.easy.sh
```

> **Answer:** Too Easy!

***

## Task 3: Introduction to IDS/IPS

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Identifying the differences between detection (IDS) and prevention (IPS) systems.

* **HIDS/HIPS:** Host-based (installed on the endpoint).
* **NIDS/NIPS:** Network-based (monitors traffic on the wire).
* **NBA:** Network Behavior Analysis (uses baselining).

Question: Which IDS or IPS type can help you stop the threats on a local machine?

> **Answer:** HIPS

Question: Which IDS or IPS type can help you detect threats on a local network?

> **Answer:** NIDS

Question: Which IDS or IPS type can help you detect the threats on a local machine?

> **Answer:** HIDS

Question: Which IDS or IPS type can help you stop the threats on a local network?

> **Answer:** NIPS

Question: Which described solution works by detecting anomalies in the network?

> **Answer:** NBA

Question: According to the official description of the snort, what kind of NIPS is it?

> **Answer:** full-blown

Question: NBA training period is also known as ...

> **Answer:** baselining

***

## Task 4: First Interaction with Snort

Testing the configuration and checking the version.

Question: Run the Snort instance and check the build number. **Command:**

```bash
snort -V
```

> **Answer:** 149

Question: Test the current instance with "/etc/snort/snort.conf" file and check how many rules are loaded. **Command:**

```bash
snort -c /etc/snort/snort.conf -T
```

> **Answer:** 4151

Question: Test the current instance with "/etc/snort/snortv2.conf" file and check how many rules are loaded. **Command:**

```bash
snort -c /etc/snort/snortv2.conf -T
```

> **Answer:** 1

***

## Task 5: Operation Mode 1: Sniffer Mode

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

* `-v`: Verbose (TCP/IP headers).
* `-d`: Dump (Packet payload).
* `-e`: Link-layer headers.

Question: Practice the parameter combinations.

> **Answer:** No answer needed

***

## Task 6: Operation Mode 2: Packet Logger Mode

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Logging packets for later analysis.

Question: Investigate traffic in ASCII mode. What is the source port used to connect port 53? **Command:**

```bash
sudo snort -dev -K ASCII -l .
# In another terminal:
sudo ./traffic-generator.sh
# Check the generated IP folder:
cd 145.254.160.237
cat UDP:3009-53
```

> **Answer:** 3009

Question: Read the snort.log file; what is the IP ID of the 10th packet? **Command:**

```bash
snort -r snort.log.1640048004 -n 10
```

> **Answer:** 49313

Question: What is the referer of the 4th packet? **Command:**

```bash
snort -r snort.log.1640048004 -n 4
```

> **Answer:** http://www.ethereal.com/development.html

Question: What is the Ack number of the 8th packet? **Command:**

```bash
snort -r snort.log.1640048004 -n 8
```

> **Answer:** 0x38AFFFF3

Question: How many "TCP port 80" packets are there? **Command:**

```bash
snort -r snort.log.1640048004 'tcp port 80'
```

> **Answer:** 41

***

## Task 7: Operation Mode 3: IDS/IPS

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Active monitoring using rulesets.

Question: What is the number of detected HTTP GET methods? **Command:**

```bash
sudo snort -c /etc/snort/snort.conf -A full -l .
# Run generator in separate terminal.
# Check alerts file:
grep "GET" alert
```

> **Answer:** 2

***

## Task 8: Operation Mode 4: PCAP Investigation

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Investigate `mx-1.pcap`. How many alerts? **Command:**

```bash
sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-1.pcap
```

> **Answer:** 170

Question: How many TCP Segments are Queued?

> **Answer:** 18

Question: How many "HTTP response headers" were extracted?

> **Answer:** 3

Question: Alerts for `mx-1.pcap` with `snortv2.conf`? **Command:**

```bash
sudo snort -c /etc/snort/snortv2.conf -A full -l . -r mx-1.pcap
```

> **Answer:** 68

***

## Task 9: Snort Rule Structure

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: Filter IP ID "35369". What is the request name? **Rule for local.rules:** `alert ip any any -> any any (msg:"ID Match"; id:35369; sid:1000001; rev:1;)` **Command:**

```bash
snort -c local.rules -A full -l . -r task9.pcap
```

> **Answer:** TIMESTAMP REQUEST

Question: Filter Syn flag. How many packets? **Rule:** `alert tcp any any -> any any (msg:"SYN"; flags:S; sid:1000002; rev:1;)`

> **Answer:** 1

Question: Filter Push-Ack flags. How many packets? **Rule:** `alert tcp any any -> any any (msg:"PA"; flags:PA; sid:1000003; rev:1;)`

> **Answer:** 216

Question: Filter UDP packets with same source and destination IP. **Rule:** `alert udp any any -> any any (msg:"SameIP"; sameip; sid:1000004; rev:1;)`

> **Answer:** 7

***

## Task 10: Snort2 Operation Logic

Understanding the `snort.conf` file is essential for tailoring the engine's behavior. This file acts as the brain of Snort, managing everything from network variables to output formats and rule paths.

#### 1. Network Variables

The most critical part of the configuration is defining your environment:

* **`HOME_NET`**: This variable defines the internal network you are protecting. It should be set to your local subnet (e.g., `192.168.1.0/24`).
* **`EXTERNAL_NET`**: This defines the untrusted network. Usually, it is set to `!$HOME_NET`, meaning "anything that is not in my home network."

#### 2. Decoder and Preprocessor Settings

Before Snort runs rules against packets, the **Decoders** normalize the traffic (handling link-layer headers) and **Preprocessors** look for non-rule-based anomalies, such as port scans or fragmented packets.

#### 3. Data Acquisition (DAQ) Modules

DAQs are abstraction layers for packet I/O. Depending on your needs, you might use:

* **Pcap:** The default for sniffing or reading files.
* **Afpacket:** Used for inline IPS mode on Linux (faster and supports memory mapped access).
* **NFQ:** Used for interacting with the Linux iptables/Netfilter queue.
* **Dump:** Used for testing; it simply dumps processed packets back to the interface.

#### 4. Rule Integration

The `include` statement is used to activate specific rulesets.

* **Site-Specific Rules:** Always include your `local.rules` to apply custom logic.
* **Rule Ordering:** Snort follows a specific order (Alert -> Pass -> Log) by default, but this can be changed in the config.

Question: Read the task above.

> **Answer:** No answer needed

***

## Task 11: Conclusion

In summary, Snort is a powerful tool for blue teamers, being able to log, sniff, detect, and prevent. However, remember to always test rules in a lab environment before deploying to production. For further mastery, it is highly recommended to reference the official Snort documentation and the TryHackMe cheatsheet to stay updated on emerging threat patterns and rule optimizations.
