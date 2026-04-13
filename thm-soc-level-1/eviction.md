---
icon: teddy-bear
cover: >-
  https://assets.tryhackme.com/additional/banners/Cyber%20Defence%20Frameworks%20icon%20and%20banner-02.svg
coverY: 0
coverHeight: 138
---

# Eviction

**Date:** 14.04.2026

**Room Category:** Challenge

**Core Concept:** Navigating the MITRE ATT\&CK framework to anticipate and defend against the specific Tactics, Techniques, and Procedures (TTPs) of APT28.

<figure><img src="../.gitbook/assets/image (160).png" alt="" width="188"><figcaption></figcaption></figure>

This challenge places us in the role of a SOC analyst who has received classified intelligence regarding an impending attack on E-corp. By proactively profiling the adversary's playbook using the MITRE ATT\&CK Navigator, we can identify necessary detections, hunt for existing indicators of compromise, and secure the network before the threat actors achieve their objectives.

***

## Threat Intelligence: APT28 (Fancy Bear)

Before diving into the matrix, it is crucial to understand the adversary we are facing. APT28 (also known as Fancy Bear, Sofacy, or STRONTIUM) is a highly sophisticated, Russian state-sponsored threat group historically attributed to Russia's General Staff Main Intelligence Directorate (GRU). They primarily target government, military, and defense-related organizations worldwide to conduct cyber espionage. APT28 is notoriously known for relying on strategic spear-phishing, credential harvesting, and custom malware (such as X-Agent) to establish deep persistence within target networks.

***

## Static Lab

{% embed url="https://static-labs.tryhackme.cloud/sites/eviction/" %}

***

## Reconnaissance & Initial Access

The initial stage of profiling involved identifying how the adversary gathers intelligence and breaks into the network. For APT28, this heavily relies on targeting user inboxes and manipulating victims into opening the door.

Question: What is a technique used by the APT to both perform recon and gain initial access?

> **Answer:** Spearphishing link

Question: Sunny identified that the APT might have moved forward from the recon phase. Which accounts might the APT compromise while developing resources?

> **Answer:** Email accounts

***

## Execution & Persistence

Once initial access is achieved, the focus shifts to how the attacker runs malicious code and maintains their foothold. APT28 utilizes built-in Windows utilities ("Living off the Land") to execute payloads and evade detection.

Question: E-corp has found that the APT might have gained initial access using social engineering to make the user execute code for the threat actor. Sunny wants to identify if the APT was also successful in execution. What two techniques of user execution should Sunny look out for? (Answer format: \<technique 1> and \<technique 2>)

> **Answer:** Malicious file and malicious link

Question: If the above technique was successful, which scripting interpreters should Sunny search for to identify successful execution? (Answer format: \<technique 1> and \<technique 2>)

> **Answer:** Powershell and Windows Command shell

Question: While looking at the scripting interpreters identified in Q4, Sunny found some obfuscated scripts that changed the registry. Assuming these changes are for maintaining persistence, which registry keys should Sunny observe to track these changes?

> **Answer:** Registry run keys

Question: Sunny identified that the APT executes system binaries to evade defences. Which system binary's execution should Sunny scrutinize for proxy execution?

> **Answer:** Rundll32

***

## Discovery & Lateral Movement

After securing a backdoor, the adversary explores the internal network and spreads to other machines. We mapped out their preferred tools for network enumeration and lateral traversal to ensure we had visibility over internal traffic.

Question: Sunny identified tcpdump on one of the compromised hosts. Assuming this was placed there by the threat actor, which technique might the APT be using here for discovery?

> **Answer:** Network sniffing

Question: It looks like the APT achieved lateral movement by exploiting remote services. Which remote services should Sunny observe to identify APT activity traces?

> **Answer:** SMB/Windows Admin shares

***

## Collection & Exfiltration

The ultimate goal of APT28 in this engagement is intellectual property theft from E-corp. We mapped their target repositories and the methods they use to obscure data exfiltration so we could sever their Command & Control (C2) lines.

Question: It looked like the primary goal of the APT was to steal intellectual property from E-corp's information repositories. Which information repository can be the likely target of the APT?

> **Answer:** Sharepoint

Question: Although the APT had collected the data, it could not connect to the C2 for data exfiltration. To thwart any attempts to do that, what types of proxy might the APT use? (Answer format: \<technique 1> and \<technique 2>)

> **Answer:** external proxy and multi-hop proxy

Question: Congratulations! You have helped Sunny successfully thwart the APT's nefarious designs by stopping it from achieving its goal of stealing the IP of E-corp.

> **Answer:** No answer needed

***

## Conclusion

Mapping an adversary's TTPs via MITRE ATT\&CK is a powerful proactive defense strategy. By understanding APT28’s reliance on spearphishing and "Living off the Land" techniques, we transitioned from reactive alert monitoring to active threat hunting. This exercise highlights that visibility into common system binaries and internal shares is just as critical as perimeter defense when facing a sophisticated APT.
