---
icon: layer-group
---

# OSI Model

Date: 01.03.2026

Room Category: Theory

Core Concept: The 7 layers of network communication

I completely agree that writing these out manually is a fast track to burnout, especially when I just want to get to the real analysis and challenge rooms. This room is pure memorization, but it's guaranteed to be on my Security+ exam this November, so having these notes handy will be a lifesaver. The OSI model is a conceptual framework that explains how data moves from an application on one computer to an application on another.

***

## Task 1: What is the OSI Model?

The OSI (Open Systems Interconnection) model breaks network communication down into seven distinct layers. A really good mnemonic to remember them from bottom to top (Layer 1 to 7) is: **P**lease **D**o **N**ot **T**hrow **S**panish **P**izza **A**way (Physical, Data Link, Network, Transport, Session, Presentation, Application).

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What does "OSI" in "OSI Model" stand for?

> Answer: Open Systems Interconnection

Question: How many layers (in digits) does the OSI model have?

> Answer: 7

Question: What is the key term for when pieces of information get added to data?

> Answer: Encapsulation

***

## Task 2: Layer 1 - Physical

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

The bottom of the barrel! The Physical layer is exactly what it sounds like - the physical equipment involved in the data transfer, like cables, fiber optics, and radio frequencies. Data here is just a raw stream of 1s and 0s (bits).

Question: What is the name of this Layer?

> Answer: Physical

Question: What is the name of the number system that is both 0's and 1's?

> Answer: Binary

Question: What is the name of the cables that are used to connect devices?

> Answer: Ethernet Cables

***

## Task 3: Layer 2 - Data Link

<figure><img src="../.gitbook/assets/image (4) (1).png" alt="" width="563"><figcaption></figcaption></figure>

While the Network layer routes data between different networks, the Data Link layer routes data _within_ the same network (like the LAN we learned about previously!). It breaks packets into "frames" and uses MAC addresses to get the data to the exact physical device on the local network. This is where switches operate.

Question: What is the name of this Layer?

> Answer: Data Link

Question: What is the name of the piece of hardware that all networked devices come with?

> Answer: Network Interface Card

***

## Task 4: Layer 3 - Network

<figure><img src="../.gitbook/assets/image (5) (1).png" alt="" width="563"><figcaption></figcaption></figure>

This is the layer where routing takes place. The Network layer takes the segments from the Transport layer and breaks them down into "packets." It figures out the best physical path for the data to reach its destination using IP addresses and routers.

It figures out the best path that data should take to reach a device by using OSPF (Open Shortest Path First) and RIP (Routing Information Protocol).

<figure><img src="../.gitbook/assets/image (6).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the name of this Layer?

> Answer: Network

Question: Will packets take the most optimal route across a network? (Y/N)

> Answer: Y

Question: What does the acronym "OSPF" stand for?

> Answer: Open Shortest Path First

Question: What doe sthe acronym "RIP" stand for?

> Answer: Routing Information Protocol

Question: What type of addresses are dealt with at this layer?/

> Answer: IP Addresses

***

## Task 5: Layer 4 - Transport

<figure><img src="../.gitbook/assets/image (7).png" alt="" width="563"><figcaption></figcaption></figure>

The Transport layer is responsible for the actual transmission of data across the network. It takes data from the session layer and breaks it into chunks called "segments." This layer is primarily dominated by two main protocols: TCP (which is reliable and checks for errors) and UDP (which is fast but doesn't care if packets get lost).

Question: What is the name of this Layer?

> Answer: Transport

Question: What does TCP stand for?

> Answer: Transmission Control Protocol

Question: What does UDP stand for?

> Answer: User Datagram Protocol

Question: What protocol guarantees the accuracy of data?

> Answer: TCP

Question: What protocol doesn't care if data is received or not by the other device?

> Answer: UDP

Question: What protocol would an application such as an email client use?

> Answer: TCP

Question: What protocol would an application that downloads files use?

> Answer: TCP

Question: What protocol would an application that streams video use?

> Answer: UDP

***

## Task 6: Layer 5 - Session

<figure><img src="../.gitbook/assets/image (8).png" alt="" width="563"><figcaption></figcaption></figure>

Whenever two devices need to talk to one another, a session needs to be created. This layer is responsible for opening the session, keeping it open and functional while data is being transferred, and closing it when the communication ends.

Question: What is the name of this layer?

> Answer: Session

Question: What is the technical term for when a connection is successfully established?

> Answer: Session

***

## Task 7: Layer 6 - Presentation

<figure><img src="../.gitbook/assets/image (9).png" alt="" width="563"><figcaption></figcaption></figure>

The Presentation layer makes sure that data is in a usable format and is where data encryption and decryption occur. If the data is compressed or encoded, it happens here so the Application layer can actually understand it.

Question: What is the name of this Layer?

> Answer: Presentation

Question: What is the main purpose that this Layer acts as?

> Answer: Translator

***

## Task 8: Layer 7 - Application

<figure><img src="../.gitbook/assets/image (10).png" alt="" width="563"><figcaption></figcaption></figure>

This is the very top layer and the only one that directly interacts with data from the user. When you open a web browser or check your email, you are interacting with protocols at the Application layer, like HTTP, HTTPS, or SMTP.

<figure><img src="../.gitbook/assets/image (11).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What is the name of this Layer?

> Answer: Application

Question: What is the technical term that is given to the name of the software that users interact with?

> Answer: Graphical User Interface

***

## Task 9: Practical - OSI Game

In this task, we have to escape a simple dungeon related to the OSI model. Upon escaping, we receive a flag as a reward.

Question: Escape the dungeon to retrieve the flag. What is the flag?

> THM{OSI\_DUNGEON\_ESCAPED}

***

## Conclusion

I'm glad to have this theory out of the way! Memorizing the OSI model can feel tedious, but understanding how data is segmented, packetized, and framed at different layers is crucial for when I eventually start doing real packet analysis and digging into PCAP files.

That's all for this room, moving on to the fun stuff!
