---
description: A walkthrough of offensive security intro
icon: swords
---

# Offensive Security Intro

Date: 26.02.2026

Room Category: Theory

Core Concept: Understanding the offensive mindset

We can't outsmart something we don't understand, this is why its important to understand offensive security. We need to learn what can break or be exploited to know how to defend them. I don't have much to say for this room because its a fairly simple one.

***

## Task 1: What is Offensive Security?

Question: Which of the following options better represents the process where you simulate a hacker's actions to find vulnerabilities in a system?

* Offensive Security
* Defensive Security

> Answer: Offensive Security

***

## Task 2: Starting the Lab

In this task, we have to start a virtual machine to 'hack' a fake bank application. A Linux virtual machine automatically pops up upon opening this question.

Question: What is your bank account number in the FakeBank web application?

> Answer: 8881

Upon opening the VM, we can see a firefox browser tab open, logged into a bank account. The account's number is 8881 and its balance in the negative, yikes.

***

## Task 3: Your First Hack

In this task, we have to open the terminal and use dirb, dirb is an offensive security tool used to brute-force a list of potential page names. Its quite simple to use as we just need to enter the following command to figure out the pages on fakebank.thm:

```bash
dirb http://fakebank.thm
```

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Looks like dirb found two pages, the bank-deposit page and the images page. The bank-deposit page has a status code of 200, meaning its accessible whereas the images page has a status code of 301, which means the page has been permanently moved from what I remember.

Question: Dirb should have found 2 hidden URLs. One of them is `http://fakebank.thm/images`. What is the other one?

> Answer: http://fakebank.thm/bank-deposit

***

## Task 4: Adding Funds to Your Account

We simply navigate to the bank-deposit page and give our account (8881) a bunch of money in this step. I'm gonna deposit myself $6767, should be enough to buy myself a good gaming setup and a couple certifications!

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Looks like we got the money, let's return to our account to go claim the flag!

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Upon returning to my account, I was greeted with this flag:

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

Question: If your balance is now positive, a pop-up should appear with some green words in it. Input the green words as the answer to this question (all in uppercase).

> Answer: BANK-HACKED

***

## Conclusion

This room was a fairly simple one, we learned the basics of dirb and 'hacked into' a bank. We also learned the importance of offensive security - you can't defend systems against attacks you don't understand!

I also decided to investigate the common.txt file on the desktop that THM mentioned, and I guess it makes sense to me. The common.txt file contains a bunch of potential webpage names that dirb will attempt to check:

<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

That's all for this room, see you!
