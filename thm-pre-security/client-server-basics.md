---
icon: display
---

# Client-Server Basics

Date: 05.03.2026

Room Category: Theory

Core Concept: Understanding basics of client-server computing

We are keeping the networking momentum going! This room is the perfect bridge between the hardware concepts we learned in "Inside a Computer" and the web protocols from "HTTP in Detail." As someone aiming to become a SOC analyst, almost every piece of network traffic I will ever look at is going to be a conversation between a client and a server. Having this mental model locked down is absolutely crucial for my Security+ exam in November, and it'll give me a huge leg up when I start at BCIT next year!

***

## Task 1: Introduction

This task gives a brief history lesson on how computers originally operated entirely alone before interconnected networks like ARPANET paved the way for the modern internet. The main objective here is to understand the client-server model at a high level.

Question: Read the introduction.

> Answer: No answer needed

***

## Task 2: Pizza Delivery

TryHackMe uses a really great pizza delivery analogy to explain this concept. If Alice orders a pizza from Linguini's, Alice is the Client (like a web browser), the pizza is the Resource (like a web page), and the restaurant is the Server.

<figure><img src="../.gitbook/assets/image (25).png" alt="" width="563"><figcaption></figcaption></figure>

The analogy maps out other network concepts perfectly too: the GPS used to find the restaurant is like DNS (translating a name to an address), the language they speak to place the order is the Protocol (like HTTP), and the specific takeaway counter at the restaurant is the Port.

Question: What do we use to identify a specific service on the server?

> Answer: Port

Question: What do we call the address of the server?

> Answer: Internet Protocol Address

***

## Task 3: Web Communication in Practice

This task drops the analogies and looks at actual HTTP/HTTPS traffic. HTTP is a "stateless" protocol, meaning every request is treated independently. We also took a look at browser developer tools (hitting F12 and checking the Network tab). Being able to read raw Request and Response headers in the Network tab is exactly what I'll be doing when investigating suspicious web traffic or analyzing malware beacons.

Question: What would the host in the following URL be?

> Answer: www.iamlearning.thm

Question: What would the scheme be?

> Answer: https

***

## Conclusion

That covers the Client-Server Computing Basics! It's a relatively straightforward concept, but understanding the flow of data from the user's endpoint to the centralized server is the foundation of network security. Getting these core concepts documented now is going to make my final HND push so much smoother by October. On to the next room!
