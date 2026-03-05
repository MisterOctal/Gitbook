---
icon: display
---

# Virtualisation Basics

Date: 05.03.2026

Room Category: Theory

Core Concept: How virtualization optimizes hardware and isolates environments.

Virtualization is a huge topic to wrap my head around as I push through the rest of my HND before October. Back in the day, the rule was "one server, one application." If a company needed a web server, a database server, and a mail server, they had to buy three massive physical computers. That is incredibly inefficient and expensive. Since I want to end up working in a SOC and eventually head off to BCIT next year, understanding how modern enterprises actually deploy their infrastructure using virtualization is essential.

***

## Task 1: Introduction

This task gives a quick overview of why virtualization exists. Physical hardware is expensive. If an application only uses 10% of a server's processing power, the other 90% is completely wasted. Virtualization was created to solve exactly this problem, allowing us to maximize hardware efficiency.

Question: Let's go!

> Answer: No answer needed

***

## Task 2: The Concept of Virtualization

TryHackMe uses a great apartment building analogy here. Instead of a single person living in a massive skyscraper (the old "one server, one app" model), virtualization divides that skyscraper into multiple independent apartments.

<figure><img src="../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure>

In this analogy, the physical server is the building itself. The individual apartments are the Virtual Machines (VMs), and the building manager who safely divides the space and allocates resources like water and electricity is called the Hypervisor.

Question: What does virtualization enable multiple applications to share?

> Answer: Physical server

Question: What is the name of the software that manages the resources for each virtual machine?

> Answer: Hypervisor

***

## Task 3: Virtualization Components

This is where we get into the technical weeds. A Hypervisor is the core technology that divides the physical hardware into isolated VMs, allocating CPU, RAM, and storage to each one.

There are two main types of hypervisors:

* Type 1 (Bare Metal): Installed directly onto the physical hardware (like VMware ESXi). This is what massive data centers use.
* Type 2 (Hosted): Installed on top of an existing Operating System (like VirtualBox running on my Windows machine). This is perfect for setting up study labs or safely analyzing malicious files.

<figure><img src="../.gitbook/assets/image (1).png" alt="" width="548"><figcaption></figcaption></figure>

We also touched on Containers, which are even more lightweight than VMs. Instead of booting up an entire virtual operating system, containers just bundle up a single application and its dependencies.

Question: Suppose a user wants to deploy a study lab on the machine to practice some exercises... which type of hypervisor will be used?

> Answer: Type 2

Question: Suppose a company wants to host multiple small applications in the same virtual machine... what should they use?

> Answer: Containers

***

## Task 4: Managing Virtual Machines

This task was a cool interactive simulation of a cloud management dashboard. We had to dig through the running hosts, look at storage capacities, and gather specific metrics about the VMs currently deployed in the environment.

Question: What is the name of the virtual machine that has been running for the longest time?

> Answer: Monitoring-SYS

Question: What is the name of the virtual machine that is using the biggest amount of memory?

> Answer: DB-Cluster-01

Question: How many VMs are in the running state after you solve the issue on mail-server?

> Answer: 8

Question: What is the name of the physical machine that is hosting most of the VMs?

> Answer: HV-PROD-02

***

## Conclusion

That covers the basics of virtualization! Knowing the difference between a Type 1 and Type 2 hypervisor, and understanding the role of containers, is going to be incredibly useful. In a modern enterprise network, almost everything I will be monitoring or defending is going to be a virtualized asset.
