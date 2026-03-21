---
icon: hammer
---

# SDLC

**Date:** 10.03.2026

**Room Category:** Walkthrough

**Core Concept:** SDLC and its importance.

This room pairs perfectly with the DevSecOps module. As a future SOC analyst, I will not be writing the software, but I absolutely need to understand how it is built, tested, and deployed. If a breach occurs through a web application, understanding the SDLC helps trace exactly where the security failure happened in the pipeline.

***

## Task 1: Introduction

This task just provides the room's objectives. In this room, we'll be learning the SDLC framework and why its important, the phases of SDLC, and how to implement security into SDLC.

Question: I'm ready to start!

> **Answer:** No answer needed

***

## Task 2: SDLC

The Software Development Lifecycle is a structured framework that defines the exact tasks performed at each step of the software creation process. Its purpose is to improve the quality of the software, meet customer expectations, and keep projects within budget and deadlines. Companies will often rearrange the cycle, splitting or unifying it into anywhere from six to eight distinct phases depending on their needs.

<figure><img src="../.gitbook/assets/image (39).png" alt="" width="563"><figcaption></figcaption></figure>

Question: How many phases can an SDLC have? (Format X-Y)

> **Answer:** 6-8

***

## Task 3: SDLC Phases Part 1

This task covers the first half of the lifecycle, taking an idea and turning it into actual code:

* **Planning:** Also known as the feasibility stage. This is where the team decides if the project makes sense and allocates the budget.
* **Requirements Definition:** This determines the first idea of the prototype and outlines exactly what the application needs to do.
* **Design and Prototyping:** This phase outlines the user interface, network requirements, and database architecture.
* **Software Development:** The actual coding phase where developers write the program.

Question: What phase focuses on determining the first idea of the prototype?

> **Answer:** Requirements Definition

Question: What stage is also known as the "Feasibility Stage"?

> **Answer:** Planning Stage

Question: When do you outline the user interfaces and network requirements?

> **Answer:** Design and Prototyping

***

## Task 4: SDLC Phases Part 2

The second half of the cycle is about making sure the code works and getting it to the users:

* **Testing:** Ensures the software meets the standards defined in the requirements phase.
* **Deployment:** Involves releasing the new version of the software to the actual users.
* **Operations and Maintenance:** Focuses on handling issues or bugs reported by end users after the software is live.

Question: What phase focuses on handling issues or bugs reported by end users?

> **Answer:** Operations and Maintenance

Question: What phase involves releasing new versions of a software?

> **Answer:** Deployment

Question: What phase ensures software meets the standards defined in the requirements phase?

> **Answer:** Testing

***

## Task 5: Keep CALMS

CALMS is a conceptual framework used to measure a company's ability to adopt DevOps processes. It stands for Culture, Automation, Lean, Measurement, and Sharing. A massive takeaway here is that you cannot simply buy tools to achieve DevOps. If a company lacks the "Culture" of collaboration or the "Sharing" of knowledge between teams, the entire process will fail.

Question: What does CALMS stand for?

> **Answer:** Culture, Automation, Lean, Measurement, Sharing

***

## Task 6: DevOps Metrics

You cannot improve what you do not measure. This task covers the key performance indicators (KPIs) that engineering teams track. For a security analyst, these metrics are incredibly revealing. If the "Production Failure Rate" is high, it means the Testing phase is failing, which almost certainly means security vulnerabilities are slipping through to the live environment. We also looked at MTTR (Mean Time to Recover), which measures how quickly a team can bounce back from an incident.

Question: What two metrics are used to measure deployment agility?

> **Answer:** Deployment speed and frequency

Question: What is an essential rate for engineers in production environments to know if the code meets security requirements?

> **Answer:** Production failure rate

Question: What is the measurement for recovery time after a failure?

> **Answer:** MTTR

***

## Task 7: Production of the Droids

This was an interactive budgeting game where we were tasked with hiring developers and setting up sprints for a project called "Empire Expects Great SDLC". The big lesson here was Brooks's Law: adding human resources to a late software project only makes it later. The winning strategy was to use a small, efficient team with a higher number of sprints, illustrating why simply throwing money and people at a failing project rarely fixes the core issues.

Question: What is the flag?

> **Answer:** THM{Ruler.of.the.SDLC.Droids}

***

## Conclusion

That concludes the SDLC room! It is a great feeling to have this documented right before my birthday. Understanding the journey that code takes from the planning whiteboard all the way to a live production server provides a ton of context for how security needs to be integrated at every single step. Time to celebrate!
