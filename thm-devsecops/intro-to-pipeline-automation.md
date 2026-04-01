---
icon: gear-complex-code
---

# Intro to Pipeline Automation

**Date:** 11.03.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding pipeline automation and how to secure it.

Happy 17th birthday to me! Starting the morning off right by looking into pipeline automation. Understanding how code travels from a developer's laptop to a live production server is a massive part of modern security. If an attacker manages to compromise the deployment pipeline itself, they can push malicious code directly into the final product without having to hack the actual web application. Knowing the architecture of these pipelines helps defenders identify where the weak links are.

***

## Task 1: Introduction

This task gives a brief welcome to the room and outlines the learning objectives. The main focus is understanding DevOps tools, automation processes, and the core security principles required to keep a pipeline safe from malicious injections.

Question: I'm ready to learn about pipeline automation and how to make it secure!

> **Answer:** No answer needed

***

## Task 2: DevOps Pipelines Explained

A pipeline is essentially an automated assembly line for software. Before learning about pipeline security, we have to define what the pipeline actually looks like. It typically starts with Source Code Storage, moves into Continuous Integration where dependencies are managed, runs through Automated Testing, and finally deploys the end product into specific Environments.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Where in the pipeline is our end product deployed?

> **Answer:** environments

***

## Task 3: Source Code and Version Control

The pipeline starts where the code is stored. Version control systems like Git allow multiple developers to work on the same project simultaneously. GitHub is the largest public host, while GitLab is frequently used by organizations that want to host their own private repositories. A huge security risk here is developers accidentally hardcoding passwords or API keys into their source code. Tools like GittyLeaks are used to automatically scan commit histories to find and eliminate these exposed secrets before they reach production.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Who is the largest online provider of Git?

> **Answer:** Github

Question: What popular Git product is used to host your own Git server?

> **Answer:** Gitlab

Question: What tool can be used to scan the commits of a repo for sensitive information?

> **Answer:** GittyLeaks

***

## Task 4: Dependency Management

Modern applications rarely write everything from scratch. They rely heavily on dependencies, which are pre-written libraries of code. Internal dependencies are created by the organization itself, while external dependencies are pulled from public repositories like PyPI (for Python) or npm (for Node.js). If an external dependency contains a vulnerability, every application using it becomes vulnerable as well. The Log4Shell vulnerability in 2021 was a prime example of how a single flawed logging dependency could compromise servers worldwide.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What do we call the type of dependency that was created by our organisation? (Internal/External)

> **Answer:** Internal

Question: What type of dependency is Jquery? (Internal/External)

> **Answer:** External

Question: What is the name of Python's public dependency repo?

> **Answer:** PyPI

Question: What dependency 0-day vulnerability set the world ablaze in 2021?

> **Answer:** Log4j

***

## Task 5: Automated Testing

Testing is fully integrated into the automated pipeline to catch issues early. We have SAST (Static Application Security Testing), which reads the raw source code to find insecure configurations without executing the program. We also have DAST (Dynamic Application Security Testing), which actively interacts with the running application to find runtime flaws. While these automated tools are fantastic for catching low hanging fruit, they completely lack human logic and cannot replace a proper manual penetration test.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What type of tool scans code to look for potential vulnerabilities?

> **Answer:** SAST

Question: What type of tool runs code and injects test cases to look for potential vulnerabilities?

> **Answer:** DAST

Question: Can SAST and DAST be used as a replacement for penetration tests? (Yea,Nay)

> **Answer:** Nay

***

## Task 6: Continuous Integration and Delivery

This task breaks down CI/CD. Continuous Integration is the practice of constantly merging code updates into a central repository. Continuous Delivery automates the release of that tested code to different environments. The pipeline relies on two main infrastructure components. The "Build Orchestrator" (like Jenkins) is the brain that manages the schedule and delegates tasks. The "Build Agent" is the actual worker node that compiles the code and runs the tests.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What does CI in CI/CD stand for?

> **Answer:** Continuous Integration

Question: What does CD in CI/CD stand for?

> **Answer:** Continuous Delivery

Question: What do we call the build infrastructure element that controls all builds?

> **Answer:** build orchestrator

Question: What do we call the build infrastructure element that performs the build?

> **Answer:** build agent

***

## Task 7: Environments

Software moves through different environments before it reaches the customer. The DEV environment is the playground where developers write and test initial code. It usually has the weakest security. The application then moves to UAT (User Acceptance Testing) and PreProd to verify functionality. Finally, it lands in PROD (Production), which is the live, highly secure environment facing the users. A major security issue occurs when developers create temporary login bypasses to test things in DEV, but accidentally leave those insecure bypasses in the code when it is pushed all the way to PROD.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Which environment usually has the weakest security configuration?

> **Answer:** DEV

Question: Which environment is used to test the application?

> **Answer:** UAT

Question: Which environment is similar to PROD but is used to verify that everything is working before it is pushed to PROD?

> **Answer:** PrePROD

Question: What is a common class of vulnerabilities that is discovered in PROD due to insecure code creeping in from DEV?

> **Answer:** Developer bypasses

***

## Task 8: Challenge

The final challenge was an interactive drag and drop exercise where we had to map the correct tools and processes to their respective places within the pipeline architecture. Placing the source code storage, dependency management, and automated testing tools in the correct logical order provided a great visual summary of the entire room.

Question: What is the flag received after successfully building your pipeline?

> **Answer:** THM{Pipeline.Automation.Is.Fun}

***

## Conclusion

That concludes the Intro to Pipeline Automation room! It is fascinating to see how the theoretical SDLC phases actually map to automated servers and scanning tools. Recognizing the difference between a build orchestrator and a build agent, and understanding how a compromised external dependency can poison a secure pipeline, is incredibly valuable knowledge. That's all for this room!
