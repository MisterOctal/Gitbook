---
icon: microchip
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/66c44fd9733427ea1181ad58-1775632461765
coverY: 0
coverHeight: 139
---

# Introduction to OT/ICS

**Date:** 04.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Understanding the fundamentals of Operational Technology (OT) and Industrial Control Systems (ICS), comparing their security paradigms to traditional IT, and highlighting the supreme importance of physical safety in these environments.

While traditional Information Technology (IT) dominates the modern workplace, a vast, hidden world of Operational Technology (OT) quietly manages the critical infrastructure relied upon daily. On a personal note, this specific sector of cybersecurity is highly intriguing due to familial ties to marine engineering, where OT systems manage shipboard operations and critical physical processes at sea.

***

## Task 1: Introduction to OT/ICS Cyber Security

This room introduces the operational differences between IT and OT/ICS, demonstrating how specialized computers generate electricity, package food, and manage water treatment facilities. Securing these environments requires an entirely different approach compared to traditional enterprise networks.

Question: Let's get started!

> **Answer:** No answer needed

***

## Task 2: What is OT/ICS?

<figure><img src="../.gitbook/assets/image (305).png" alt="" width="375"><figcaption></figcaption></figure>

**Operational Technology (OT)** OT is the use of computers to monitor and control physical equipment and processes in the real world. Examples include medical equipment (MRI scanners), building management systems (HVAC, elevators), and embedded computers in automobiles (Anti-lock Braking Systems).

<figure><img src="../.gitbook/assets/image (307).png" alt="" width="375"><figcaption></figcaption></figure>

**Industrial Control Systems (ICS)** ICS is a specific subset of OT found exclusively in industrial environments. Examples include power plants, oil refineries, steel mills, and pharmaceutical manufacturing facilities. (Note: Hospitals and cars use OT, but not ICS).

<figure><img src="../.gitbook/assets/image (308).png" alt="" width="375"><figcaption></figcaption></figure>

**Supervisory Control and Data Acquisition (SCADA)** SCADA is a type of ICS used over a wide-area network (WAN) to monitor and control remote equipment and processes. WAN technologies include 5G, microwave radio links, and satellites. SCADA is utilized for geographically dispersed infrastructure like offshore oil rigs, offshore wind farms, and pipeline pumping stations.

Question: What does the 'O' in OT stand for?

> **Answer:** Operational

Question: What does the 'C' in ICS stand for?

> **Answer:** Control

***

## Task 3: How Does OT/ICS Work?

OT systems monitor and manipulate physical processes in the real world.

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1775633247982.png" alt=""><figcaption></figcaption></figure>

**Programmable Logic Controllers (PLCs)** PLCs are the most common type of OT asset deployed today. They operate using programmed logic to read inputs and trigger outputs, similar to how a home thermostat reads room temperature (input) and turns on the air conditioning unit (output) when the temperature exceeds a specific setpoint.

* **Inputs:** Pathways that bring data into the PLC from sensors. Sensors measure environmental conditions such as temperature, tank capacity, pressure, or vibration patterns.
* **Outputs:** Pathways that allow the PLC to send signals to physical systems in the real world, such as pumps, valves, and motors.

Question: When a PLC needs to send a signal to turn off a motor in the real world, it sends a signal through what type of connection?

> **Answer:** Output

Question: What source provides the environmental data that inputs feed into a PLC?

> **Answer:** Sensor

***

## Task 4: What is OT/ICS Cyber Security?

Modern OT/ICS environments increasingly leverage traditional IT technologies (Ethernet, TCP/IP, Cloud) to improve efficiency. However, this convergence introduces vulnerabilities into systems that were traditionally isolated.

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1775633247982.png" alt=""><figcaption></figcaption></figure>

**IT vs. OT Infrastructure Comparison:**

* **Primary Focus:** IT manages data/information; OT manages physical processes/machinery.
* **Top Priority:** IT prioritizes Confidentiality; OT prioritizes Safety (human and environmental).
* **Asset Lifespan:** IT hardware is replaced every 3 to 5 years; OT hardware operates for 10 to 30+ years, making legacy systems extremely common.
* **Patching:** IT utilizes regular, scheduled updates; OT patching is rare and difficult, requiring expensive production downtime.
* **Response to Failure:** Rebooting an IT system is acceptable; rebooting an OT system can cause physical damage or safety incidents.

Human operators monitor these physical processes via a **Human Machine Interface (HMI)**. An attacker gaining access to an HMI could theoretically stop power generation, poison water supplies, or cause physical destruction. The 2021 Colonial Pipeline incident served as a major turning point, after which cyberattacks targeting OT/ICS networks doubled annually.

Question: What type of system is used by a human operator to interact with a PLC and control a physical process in the real world?

> **Answer:** Human Machine Interface

Question: Which 2021 incident marked a turning point for OT/ICS security, after which annual cyberattacks against these networks doubled?

> **Answer:** Colonial Pipeline

***

## Task 5: Differences Between OT & IT Cyber Security

While IT and OT share common networking protocols, their security priorities are fundamentally different.

**The Security Triads:**

{% columns %}
{% column %}
<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1775634014465.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}

{% column %}
<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1775634014643.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

* **IT Cyber Security:** Governed by the C-I-A triad (Confidentiality, Integrity, Availability).
* **OT Cyber Security:** Governed by the A-I-C triad (Availability, Integrity, Confidentiality), but with **Physical Safety** superseding all three. Ensuring that on-site employees and the surrounding public are not harmed dictates every defensive action taken in an OT environment.

**Operational Differences:**

* **Patch Management:** IT patches automatically; OT patches rarely, usually only during scheduled annual maintenance windows.
* **Vulnerability Scanning:** IT uses active scanning (Nmap, Nessus); OT relies on passive monitoring, as active scanning can cause fragile PLCs to crash.
* **Incident Response:** IT isolates and reboots compromised systems; OT attempts to keep the system running to avoid introducing safety hazards.
* **Network Encryption:** IT encrypts almost everything; OT environments rarely leverage encryption, as older legacy protocols do not support it and plants favor uninterrupted availability over confidentiality.

Question: What is the most important requirement for OT/ICS cyber security?

> **Answer:** Safety

Question: What do many OT environments not leverage?

> **Answer:** Encryption

***

## Task 6: What a Human Operator Sees in OT Environments

This practical exercise involves interacting with a simulated HMI to observe how human operators monitor tank levels, receive warnings, and interact with pumps and valves to maintain safe operational states.

Question: Based on a review of the HMI, what type of environment is this?

> **Answer:** ICS

Question: When you first look at the HMI, what is the current percentage level indicated by the tank level sensor?

> **Answer:** 65

Question: Click on the START button to turn on the pump to bring more water into the tank. At what percentage level do you first receive an alert in a yellow warning banner?

> **Answer:** 85

Question: Continue to allow water to flow into the tank. At what percentage level does the control system realize there is danger and shuts off the pump bringing water into the tank?

> **Answer:** 95

Question: With the pump stopped, click OPEN on the valve on the outtake pipe. What happens to the water level in the tank? The water level .

> **Answer:** Lowers

***

## Conclusion

Securing Operational Technology requires abandoning standard IT assumptions and adopting a safety-first mindset. This room, created by Mike Holcomb, successfully demonstrated the distinctions between IT, OT, ICS, and SCADA, laying the groundwork for advanced industrial control system defense.
