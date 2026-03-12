---
icon: shield
---

# Source Code Security

Date: 12.03.2026

Room Category: Independent Study

Core Concept: Understanding version control systems, Git, and how to secure credentials in code repositories.

New day, new room. Continuing on the DevSecOps path, this room focuses on Source Code Security. Source code is the blueprint of any application, and protecting it is critical. Often, developers accidentally hardcode credentials or API keys directly into their commits, which attackers can easily harvest. Learning how version control systems like Git operate and how to properly manage secrets is a fundamental skill for any security engineer.

***

## Task 1: Introduction

This task sets the stage for the room. Protecting source code ensures the integrity and confidentiality of an application. We will be looking at version control systems, specifically Git, to understand how code is managed and where security vulnerabilities typically arise.

Question: I'm ready!

> Answer: No answer needed

***

## Task 2: Git and Linus

Git was created by Linus Torvalds in 2005 for the development of the Linux kernel. Before Git, the Linux project used a proprietary system called BitKeeper. When that relationship soured, Torvalds built Git to be open source, fast, and capable of handling massive, distributed projects.

Question: When was Git released?

> Answer: 2005

Question: What did Linux Kernel use for a DVCS previous to git?

> Answer: BitKeeper

***

## Task 3: Version Control Concepts

Version control systems track changes made to code over time. They allow multiple developers to collaborate without overwriting each other's work. Git is a "Distributed" Version Control System (DVCS), meaning every developer has a full local copy of the entire repository history, unlike centralized systems where the history lives only on one main server.

Question: What type of version control is Git?

> Answer: Distributed

***

## Task 4: Cloud Based Version Control

To collaborate globally, teams use cloud based hosting platforms for their Git repositories. GitHub, founded in 2008, is the most popular, but GitLab and Bitbucket are heavily used in enterprise environments. These platforms host the repositories and integrate heavily into the CI/CD pipelines we learned about previously.

Question: When was Github founded?

> Answer: 2007

Question: Where do Cloud-Based VCS store code?

> Answer: repositories

***

## Task 5: Insufficient Credential Hygiene

This is the core security issue. Attackers actively scan code repositories for hardcoded passwords and API keys. To prevent this, developers should use environment variables. Instead of typing a password into the code, the code references a variable that exists only on the secure host machine. However, environment variables alone are not a silver bullet and can still be compromised if the host is breached or if the variables are accidentally logged.

Question: What is a solution to store secrets securely without revealing them?

> Answer: environment variables

Question: Does using environment variables mean you are free from secrets being compromised? (Yes or No)

> Answer: No

***

## Task 6: The Git, The Branch and The Ugly

This task covers fundamental Git commands. "Branches" are used to isolate lines of development so experimental features do not break the main application. "Origin" is the default name given to the remote repository you cloned from. Using the clone command copies the entire remote repository to your local machine.

<figure><img src="../.gitbook/assets/image (46).png" alt="" width="540"><figcaption></figcaption></figure>

Question: How does Git refer to isolated lines of development?

> Answer: branches

Question: What term does Git use to refer to the original repository you cloned from?

> Answer: origin

Question: What command can you use to "copy" the contents in a remote repository?

> Answer: git clone

***

## Task 7: USCSS Nostromo

This was a practical exercise using a simulated GitLab environment. We had to clone a repository for the "USCSS Nostromo" project, identify a hardcoded credential in the application file, and replace it with an environment variable. After fixing the code, we pushed the changes back to the repository.

Unfortuantely I forgot to take a screenshot during this task but we have to navigate a given repository to examine the hardcoded flaw and fix it.

Question: What is the name of the package that you need to import to make use of os.getenv?

> Answer: os

Question: What is the hidden flag?

> Answer: THM\_3LL3N-RIPL3Y

***

## Task 8: Secret Management

While environment variables are better than hardcoded text, enterprise environments require dedicated Secret Management solutions to safely rotate and audit credential access. We also looked at how GitLab handles CI/CD configurations through a specific YAML file, which is crucial for automating secure build pipelines.

Question: What is the hidden flag?

> Answer: THM\_S3CUr3\_4L13NS

Question: What do you need to keep source code secure besides environment variables?

> Answer: secret management

Question: Which file handles the configuration to run CI/CD jobs?

> Answer: .gitlab-ci.yml

***

## Conclusion

That wraps up the Source Code Security room! Understanding how Git tracks changes and how developers manage credentials is a critical perspective. If an attacker gains access to a repository, the blast radius is massive. Learning how to properly audit commits and enforce credential hygiene directly translates to securing the early stages of the deployment pipeline. A great start to being 17!
