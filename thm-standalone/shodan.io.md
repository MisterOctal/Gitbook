---
icon: nfc-magnifying-glass
---

# Shodan.io

**Date:** 09.03.2026

**Room Category:** Walkthrough

**Core Concept:** Using Shodan.io and its benefits

Tomorrow is the first day of my HND Computing Research Project, its very likely that I'll be using Shodan.io to gather research data during the project. I also have some experience with Shodan and I've used it before for local research. Fortunately, I also have the education membership so I won't be limited or constricted on here.

***

## Task 1: Introduction

Shodan.io is basically the Google of IoT. You can find publicly accessible security cameras, what servers are being used, and more! It uses a web crawler to scan the whole internet and archive the services that run on each IP address, extremely useful for researching.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In this example, I used Shodan to see what's going on in my country (Myanmar), as we can see, Yangon seems to have the most IoT devices. This makes sense, its the largest city in Myanmar and where I live after all. We can also see that Mytel, which is a government-controlled telcom company is the largest organization here.

Question: Go to Shodan.io

> **Answer:** No answer needed

***

## Task 2: Filters

In this task, we learn about the usage of filters on Shodan. There is an extensive list of filters that we can use on Shodan, but some of the popular ones include product, country, port, and asn. Another powerful filter is the vuln filter, which we can use to find ip addresses that are vulnerable to certain vulnerabilities.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In this example, I used Shodan to find out how many IPs in the US, part of Digital Ocean's ASN are still vulnerable to Eternal blue. Fortunately, only 47 are vulnerable in a country with millions of devices on Shodan, so that's good.

Question: What command is used to find Eternal Blue exploits on Shodan using the vuln filter?

> vuln:ms17-010

***

## Task 3: Google & Filtering

In this task, we will use Shodan and its filters to examine Google and it services. Unfortunately, this room was made a few years ago so the data might not be accurate anymore and TryHackMe has provided the answers so we can skip this task.

Question: What is the top operating system for MYSQL servers in Google's ASN?

> **Answer:** 5.6.40-84.0-log

Question: What is the 2nd most popular country for MYSQL servers in Google's ASN?

> **Answer:** Netherlands

Question: Under Google's ASN, which is more popular for nginx, Hypertext Transfer Protocol or Hypertext Transfer Protocol with SSL?

> **Answer:** Hypertext Transfer Protocol

Question: Under Google's ASN, what is the most popular city?

> **Answer:** Kansas City

Question: Under Google's ASN in Los Angeles, what is the top operating system according to Shodan?

> **Answer:** Debian

Question: Using the top Webcam search from the explore page, does Google's ASN have any webcams? Yay / Nay.

> **Answer:** Nay

***

## Task 4: Shodan Monitoring

Shodan Monitor is a security service that actively monitors an IP address and provides real-time alerts about open ports, services, or vulnerabilities. Shodan will also automatically email you with any updates regarding this.

I will likely have to use Shodan Monitor in my computing research project, but we'll have to wait and see!

Question: What URL takes you to Shodan Monitor?

> **Answer:** [https://monitor.shodan.io/dashboard](https://monitor.shodan.io/dashboard)

***

## Task 5: Shodan Dorking

Shodan Dorking allows you to find some interesting webpages. For instance, using the filter: `has_screenshot:true encrypted attention` allows you to see devices that are infected with ransomware.

Question: What dork lets us find PCs infected by Ransomware?

> **Answer:** has\_screenshot:true encrypted attention

***

## Task 6: Shodan Extension

Shodan also has an extension, when installed it can tell you basic information about the webserver and if there are any major issues. It seems like this extension is very usable for people interested in bug bounties.

Question: This will be nice for bug bounties!

> **Answer:** No answer needed

***

## Conclusion

Shodan is a vital tool for understanding the external attack surface of any organization. This room demonstrates how simple searches and filters can reveal critical infrastructure exposures, ranging from misconfigured web servers to vulnerable industrial control systems. For anyone moving into a defensive role, mastering Shodan is essential for proactive threat hunting and asset discovery. That's all for today!
