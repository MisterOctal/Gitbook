---
icon: cloud
---

# Cloud Computing Fundamentals

Date: 05.03.2026

Room Category: Theory

Core Concept: Cloud deployment models, service types, and cost management.

This room is a perfect follow up to the virtualization stuff we just covered. In the real world, almost every enterprise environment is either fully in the cloud or running a hybrid setup. As a future SOC analyst, I am going to have to know how to monitor and secure cloud infrastructure just as much as on premise networks. This room will also give me a head start on my HND Cloud Computing Module.

***

## Task 1: Introduction

This task gives a quick intro to why the cloud even exists. If you have an application that suddenly goes viral, a single local server will crash under the load. The cloud solves this by using the virtualization and container technologies we learned about previously to seamlessly spread that load across massive, shared data centers.

Question: Let's go!

> Answer: No answer needed

***

## Task 2: Cloud Computing Overview

Here we look at the evolution of servers. We started with massive physical mainframes in the 1960s, moved to virtualization in the late 90s, and finally arrived at the modern cloud era around 2012. Cloud computing basically lets a business consume computing resources over the internet instead of having to buy, power, and cool their own hardware.

<figure><img src="https://cdn.discordapp.com/attachments/1472776768103518241/1479481031575732337/image.png?ex=69ac3195&#x26;is=69aae015&#x26;hm=d34698e733d6dd51e020e60af1dbf2db75f593341eead874899e5889ccfcbb15" alt="" width="563"><figcaption></figcaption></figure>

The main benefits of the cloud are also included. They are: scalability (growing on demand), high availability, and paying only for what you use. We also looked at the different deployment models (Public, Private, and Hybrid) and the service models (IaaS, PaaS, SaaS).&#x20;

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/68dac5d6d4d4f23175b3296f/room-content/68dac5d6d4d4f23175b3296f-1765978997643.png" alt="" width="563"><figcaption></figcaption></figure>

The apartment analogy they used was great: IaaS is renting an empty apartment, PaaS is renting a semi-furnished apartment, and SaaS is just staying in a fully serviced hotel.

Question: What is the characteristic of a cloud environment that enables you to handle an unexpected increase in traffic?

> Answer: Scalability

Question: What is the most common type of cloud deployment used?

> Answer: Public Cloud

Question: Suppose you want to deploy an application to the internet, focusing only on application development and leave the infrastructure to others. Which cloud service model would you use?

> Answer: PaaS

***

## Task 3: Deploying a Cloud Instance

This task was a really cool interactive simulation of a cloud billing dashboard (which looks very similar to AWS or Google Cloud). It drives home the point that cloud resources charge by the minute or hour based on their size. You have to be super careful when spinning up massive instances like an M5 Large, otherwise the bill skyrockets.

Question: What is the total cost of credit if Study Machine 1 and Study Machine 2 are stopped?

> Answer: 30

Question: How many credits does an M5 Large instance cost?

> Answer: 70

Question: What is the total cost of credit if only the new instances we created are running?

> Answer: 150

Question: What would the total running cost of the environment be if a third T3 Small study machine were added alongside our machines?

> Answer: 188

***

## Conclusion

That covers the Cloud Computing Fundamentals! Understanding the difference between IaaS, PaaS, and SaaS is vital because the security responsibilities change completely depending on which one you use. In IaaS, the SOC team has to monitor the virtual operating system, but in SaaS, they only monitor user access and data. This was a great primer before diving into more technical cloud security topics!
