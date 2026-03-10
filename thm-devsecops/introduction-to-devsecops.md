---
icon: hammer
---

# Introduction to DevSecOps

Date: 10.03.2026

Room Category: Walkthrough

Core Concept: DevSecOps as a culture and discipline.

I decided to branch out into other paths to avoid fatigue, DevSecOps is a really interesting transition. Even as a future SOC analyst, I need to understand how the applications I am monitoring were actually built. In the past, security was a separate gate at the very end of development. DevSecOps changes that by making security a shared responsibility from day one. This knowledge helps me understand where vulnerabilities are likely to be introduced in the pipeline.

***

## Task 1: Introduction

This task provides an overview of the room's learning objectives. We are looking at the history behind software development practices, how they have evolved over the years, and the impact they have on the security industry as a whole.

Question: I'm ready to start!

> Answer: No answer needed

***

## Task 2: DevOps: A New Hope

To understand DevSecOps, you have to look at the history of software development. We started with the Waterfall model, a strict, hierarchical approach where developers, testers, and admins worked in total silos, leading to massive backlogs and poor communication.&#x20;

<figure><img src="../.gitbook/assets/image (30).png" alt="" width="375"><figcaption></figcaption></figure>

The Agile methodology later took over, focusing on collaboration and flexibility.&#x20;

<figure><img src="../.gitbook/assets/image (31).png" alt="" width="563"><figcaption></figcaption></figure>

Finally, DevOps was born to unite both development and operations through heavy automation. It broke down the "wall of confusion" between developers who wanted fast changes and operations teams who wanted stability, building a shared culture of trust.

Question: What methodology relies on self-organising teams that focus on constructive collaboration?

> Answer: Agile

Question: What methodology relies on automation and integration to drive cultural change and unite teams?

> Answer: DevOps

Question: What traditional approach to project management led to mistrust and poor communication between development teams?

> Answer: Waterfall

Question: What does DevOps emphasize?

> Answer: Building Trust

***

## Task 3: The Infinite Loop

DevOps is visualized as an infinite loop consisting of planning, coding, building, testing, releasing, deploying, operating, and monitoring. Automation is the absolute engine that keeps this loop moving efficiently.

<figure><img src="../.gitbook/assets/image (32).png" alt="" width="563"><figcaption></figcaption></figure>

We use tools like CI/CD (Continuous Integration and Continuous Deployment) to automate the heavy lifting. Continuous Integration means developers are merging their code changes into a central repository daily, which automatically triggers unit tests. Continuous Deployment takes that tested code and automatically pushes it to production servers.&#x20;

We also use IaC (Infrastructure as Code) to provision environments. Using tools like Terraform, we can write a text file that defines an entire cloud network, allowing us to build and tear down servers in minutes while ensuring configurations are consistent every single time.

Question: What helps in adding tests in an automated manner and deals with the frequent merging of small code changes?

> Answer: CI/CD

Question: What process focuses on collecting data to analyse the performance and stability of services?

> Answer: Monitoring

Question: What is a way to provision infrastructure through reusable and consistent pieces of code?

> Answer: IaC

***

## Task 4: Shifting Left

The core philosophy of DevSecOps is "Shifting Left." In older models, security testing was a massive bottleneck that happened right before a product was scheduled for release. If a critical vulnerability was found at the last minute, the entire project had to be delayed, costing the company a lot of money and causing friction between teams.

<figure><img src="../.gitbook/assets/image (33).png" alt="" width="563"><figcaption></figcaption></figure>

Shifting left means moving security checks into the earliest planning and coding phases. By catching flaws in the developer's IDE or during the automated build process, remediation is incredibly cheap and fast. This is usually achieved by automating SAST (Static Application Security Testing) to scan raw code, SCA (Software Composition Analysis) to check third party libraries for known CVEs, and DAST (Dynamic Application Security Testing) to attack the running application just like a hacker would.

Question: What term is it used to describe accounting for security from the earliest stages in a development lifecycle?

> Answer: Shift Left

Question: What is the development approach where security is introduced from the early stages of a development lifecycle until the final stages?

> Answer: DevSecOps

***

## Task 5: DevSecOps: Security Strikes Back

Integrating security into the fast paced DevOps environment brings unique challenges. The biggest hurdles are security silos, where security operates as an isolated gatekeeper with specialized knowledge that no one else possesses. This creates a severe lack of visibility and poor prioritization across different teams.

Furthermore, stringent, manual security processes completely ruin the speed that DevOps provides. If a developer has to wait three weeks for a manual security audit on a minor feature update, they will simply find ways to bypass security entirely. To fix this, security must adapt to be a supportive, automated function that integrates directly into the developer's existing tools rather than acting as a constant roadblock.

Question: What DevSecOps challenge can lead to a siloed culture?

> Answer: Security Silos

Question: What DevSecOps challenge can affect not prioritizing the right risks at the right times?

> Answer: Lack of Visibility

Question: What DevSecOps challenge stems from needlessly overcomplicated security processes?

> Answer: Stringent Processes

***

## Task 6: DevSecOps Culture

DevSecOps is often said to be 80 percent culture and 20 percent tooling. You cannot just buy a security scanner and claim you are doing DevSecOps. It only works if teams actively communicate and share the responsibility of securing the application.

To fix the organizational challenges from the previous task, security teams must promote the autonomy of developers by training them to write secure code from the start. They must provide complete transparency regarding what the security tools are actually doing and demonstrate understanding and empathy for the strict deadlines that product teams face. Instead of throwing thousands of unverified alerts over the fence, security needs to provide prioritized, actionable feedback.

Question: How can you make security scalable so it's not left behind when start ups face hypergrowth or in large corporations?

> Answer: Promote autonomy of teams

Question: How can you support teams in understanding risk and educating on security flaws?

> Answer: Visibility and Transparency

Question: What are key factors to successfully instill security in the development process by accounting for flexibility?

> Answer: Understanding and Empathy

***

## Task 7: Exercise: Fuel Trouble

As a huge star wars fan, this room and its art is very charming to me. In this task, we have to deploy a static site to view a comic to answer the questions. Its a great visual summary of the operational differences between the methodologies.

The first comic represents the waterfall model.

<figure><img src="../.gitbook/assets/image (34).png" alt="" width="563"><figcaption></figcaption></figure>

The second comic represents the agile model.

<figure><img src="../.gitbook/assets/image (36).png" alt="" width="563"><figcaption></figcaption></figure>

The last comic represents the DevOps model.

<figure><img src="../.gitbook/assets/image (37).png" alt="" width="563"><figcaption></figcaption></figure>

This site perfectly highlights the rigid, slow planning of the Waterfall approach, the adaptive but sometimes disorganized teamwork of the Agile method, and the smooth, highly automated continuous delivery of DevOps. This interactive element really had me hooked.

Question: What Software Development Model did the team in Comic 1 follow?

> Answer: Waterfall

Question: What Software Development Model did the team in Comic 2 follow?

> Answer: Agile

Question: What Software Development Model did the team in Comic 3 follow?

> Answer: DevOps

Question: What is the flag?

> Answer: THM{ONE\_TWO\_THREE}

***

## Conclusion

That wraps up the Introduction to DevSecOps! It is a massive shift in mindset from traditional IT security. Understanding how automation and the shift left mentality work is vital for anyone entering the industry today. Knowing the difference between Agile, Waterfall, and DevOps will give me much more context when reviewing applications or tracking down how a vulnerability was introduced into a production environment. A great, theory heavy break from the standard labs!
