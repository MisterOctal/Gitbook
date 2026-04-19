---
icon: hammer
---

# SSDLC

**Date:** 10.03.2026

**Room Category:** Walkthrough

**Core Concept:** Integrating security into every phase of the Software Development Lifecycle.

A quick follow up to the standard SDLC room before my birthday tomorrow! We just looked at how software is built, but this room is about how to build it securely from the ground up. In a SOC, knowing how an engineering team handles threat modeling and risk assessments during development gives massive context when triaging vulnerabilities in production. The goal here is to understand the Secure Software Development Lifecycle (SSDLC) and the methodologies used to shift security left.

***

## Task 1: Introduction

This task sets the stage for the room. Secure SDLC is all about bringing security in as early as possible rather than bolting it on at the end of a project.

Question: Let's get this bread

> **Answer:** No answer needed

***

## Task 2: What is SSDLC?

This task highlights the financial and operational costs of ignoring security. Finding a bug or vulnerability during the testing or maintenance phase is exponentially more expensive and time consuming than catching it during the design phase. By implementing an SSDLC, companies save money, prevent delays, and drastically reduce their overall risk.

A study conducted by IBM discovered that it can cost around six times more to fix a bug during implementation, 15 times during testing, and up to 100 times during maintenance in the diagram below.

<figure><img src="../.gitbook/assets/image (40) (1).png" alt="" width="418"><figcaption></figcaption></figure>

Question: How much more does it cost to identify vulnerabilities during the testing phase?

> **Answer:** 15

***

## Task 3: Implementing SSDLC

Before diving into security tools, an organization has to understand its current security posture. This means performing a gap analysis and creating formal policies. Once the baseline is established, security processes are mapped to the existing SDLC phases. For example, Risk Assessments happen during the planning phase, while Threat Modeling takes place during the design phase.

<figure><img src="../.gitbook/assets/image (41) (1).png" alt="" width="555"><figcaption></figcaption></figure>

Question: What should you understand before implementing Secure SDLC processes?

> **Answer:** Security posture

Question: During which stages should you perform a Risk Assessment?

> **Answer:** Planning and requirements

Question: What should be carried out during the design phase?

> **Answer:** Threat modelling

***

## Task 4: Risk Assessment

This phase evaluates the likelihood of a threat being exploited and the potential impact it would have. There are two main ways to measure this. Qualitative risk assessments use subjective categories like Low, Medium, and High based on the formula of Severity multiplied by Likelihood. Quantitative risk assessments assign actual numerical and monetary values to the risk, calculating metrics like the Annual Loss Expectancy to decide if a security control is actually worth the investment.

<figure><img src="../.gitbook/assets/image (42) (1).png" alt="" width="402"><figcaption></figcaption></figure>

Question: What is a formula to assign a Qualitative Risk level?

> **Answer:** Severity x likelihood

Question: Which type of Risk Assessment assigns numerical values to determine risk?

> **Answer:** Quantitative risk assessment

***

## Task 5: Threat Modelling

Threat modeling is a structured process of identifying potential vulnerabilities and prioritizing mitigations before a single line of code is written. We looked at three main frameworks. STRIDE evaluates threats based on Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, and Elevation of Privilege (tying directly to the CIA triad). DREAD assigns a rating system based on damage and reproducibility. Finally, PASTA is a risk centric framework that aligns technical requirements directly with business objectives.

<figure><img src="../.gitbook/assets/image (43) (1).png" alt="" width="555"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (44) (1).png" alt="" width="428"><figcaption></figcaption></figure>

Question: What threat modelling methodology assigns a rating system based on risk probability?

> **Answer:** DREAD

Question: What threat modelling methodology is built upon the CIA triad?

> **Answer:** STRIDE

Question: What threat modelling methodology helps align technical requirements with business objectives?

> **Answer:** PASTA

***

## Task 6: Secure Coding

Once the developers start writing the actual application, automated code analysis tools step in. SAST (Static Application Security Testing) is a white-box testing method that analyzes the raw source code for vulnerabilities without running it, making it perfect for the early stages. DAST (Dynamic Application Security Testing), on the other hand, is a black-box method that attacks the application while it is running to find runtime flaws that SAST might miss.

<figure><img src="../.gitbook/assets/image (45) (1).png" alt="" width="560"><figcaption></figcaption></figure>

Question: Is it recommended to use SAST analysis at the beginning of the SDLC? (y/n)

> **Answer:** y

Question: Which type of code analysis uses the black-box method?

> **Answer:** DAST

Question: Which type of code analysis uses the white-box method?

> **Answer:** SAST

***

## Task 7: Security Assessments

After development, the software must be thoroughly tested. Vulnerability Assessments use automated scanners to quickly find known flaws, making them budget friendly but prone to false positives. Penetration Testing goes much deeper. It involves human testers attempting to actively exploit the vulnerabilities to demonstrate real world impact. Both of these are typically carried out during the Operations and Maintenance phase to ensure the live environment remains secure.

Question: Which form of assessment is more budget-friendly and takes less time?

> **Answer:** Vulnerability Assessment

Question: Which type of assessment identifies vulnerabilities and attempts to exploit them?

> **Answer:** Penetration Testing

Question: When do you typically carry out Vulnerability Assessments or Pentests?

> **Answer:** Operations & Maintenance

***

## Task 8: SSDLC Methodologies

This task covers the high level frameworks organizations use to guide their secure development. Microsoft SDL embeds mandatory security procedures directly into the pipeline. We also looked at maturity models, which act as a measuring stick for a company's security posture. SAMM (Software Assurance Maturity Model) helps organizations build tailored strategies, while BSIMM measures real world software security initiatives by comparing them against other companies in the industry.

Question: What methodology follows a set of mandatory procedures embedded in the SDLC?

> **Answer:** Microsoft SDL

Question: What Maturity Model helps you measure tailored risks facing your organisation?

> **Answer:** SAMM

Question: What maturity model acts as a measuring stick to determine your security posture?

> **Answer:** BSIMM

***

## Task 9: Secure Space Lifecycle

This was an interactive mini game where we had to pilot a spaceship and correctly map the security procedures to the right phases of the software development lifecycle. It was a fun way to lock in the concepts, matching things like risk assessments to the planning phase and code reviews to the development phase.

Question: What is the flag?

> **Answer:** THM{D0-A-Barr3l-R011}

## Conclusion

That wraps up the Secure SDLC room! It is really helpful to see exactly how concepts like threat modeling and vulnerability scanning fit into the broader engineering workflow. Understanding these frameworks gives me a much clearer picture of how enterprise security teams actually operate alongside developers. Time to log off and get ready to turn 17 tomorrow!
