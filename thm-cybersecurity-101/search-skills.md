---
icon: magnifying-glass
---

# Search Skills

Date: 17.03.2026

Room Category: Walkthrough

Core Concept: Learning how to efficiently find technical information, security documentation, and threat intelligence using various search engines and specialized online tools.

The ability to quickly and accurately find information online is arguably the most important skill in cybersecurity. Whether you are debugging an error message, researching a new vulnerability, or gathering open source intelligence (OSINT) on a target, knowing exactly which search engine to use and how to filter your queries saves a massive amount of time.

***

## Task 1: Introduction

This room focuses on teaching you how to find the right information online efficiently using different tools and resources. It covers general search engines like Google, specialized search engines like Shodan, technical documentation, and vulnerability databases.

Question: Check how many results you get when searching for **learn hacking**. At the time of writing, we got 1.5 billion results when searching on Google.

> Answer: No answer needed

***

## Task 2: Evaluation of Search Results

Searching is easy, but finding exactly what you need requires properly evaluating the results you get back. It is important to look at the sheer volume of results to gauge how broad your search is. If you get millions of hits, you likely need to refine your keywords or use search operators to narrow things down.

<figure><img src="../.gitbook/assets/image (55).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What do you call a cryptographic method or product considered bogus or fraudulent?

> Answer: snake oil

Question: What is the name of the command replacing netstat in Linux systems?

> Answer: ss

***

## Task 3: Search Engines

General search engines like Google or DuckDuckGo are the starting point for most research. Knowing how to use search operators (like `filetype:`, `site:`, or using quotation marks for exact matches) allows you to filter out irrelevant results and pinpoint exactly what you are looking for. You can also use them to define technical terms or find modern alternatives to deprecated command line tools.

Question: How would you limit your Google search to PDF files containing the terms "cyber warfare report"?

> Answer: filetype:pdf cyber warfare report

Question: What phrase does the Linux command ss stand for?

> Answer: socket statistics

***

## Task 4: Specialized Search Engines

While Google indexes standard web pages, specialized search engines index specific types of data and internet connected devices. Shodan, for example, is a search engine for IoT devices, servers, webcams, and industrial control systems. VirusTotal acts as a specialized search engine for malware, allowing you to search for a file hash to see if it has been previously flagged by security vendors. Lastly, have I been pwned? tells you if an email address has been leaked in a data breach.

Question: What is the top country with Lighty servers?

> Answer: United States

Question: What does BitDefender FKS detect the file with the hash provided in the room as?

> Answer: Android.Riskware.Agent.LHH

***

## Task 5: Vulnerabilities and Exploits

When a new software flaw is discovered, it is assigned a Common Vulnerabilities and Exposures (CVE) identifier. Databases like the MITRE CVE list or the National Vulnerability Database (NVD) allow security analysts to look up these IDs to understand the technical details of the vulnerability, its severity score, and how it can be patched or exploited.

Question: What utility does the CVE mentioned in the task refer to?

> Answer: xz

***

## Task 6: Technical Documentation

No one memorizes every command or configuration setting. Reading technical documentation, vendor manuals, or Linux `man` pages is a daily requirement in IT and security. Knowing how to efficiently navigate these manuals to find specific command flags or configuration syntax is critical for setting up and securing systems.

Question: What does the Linux command cat stand for?

> Answer: concatenate

Question: What is the netstat parameter in Microsoft Windows that displays the executable associated with each active connection and listening port?

> Answer: -b

***

## Task 7: Social Media

Social media platforms are a goldmine for Open Source Intelligence (OSINT). Attackers often use platforms like LinkedIn to map out a company's organizational chart or identify technologies used by reviewing employee skills. They also use Facebook or Instagram to find answers to common password reset questions (like a pet's name or hometown).

Question: You are hired to evaluate the security of a particular company. What is a popular social media website you would use to learn about the technical background of one of their employees?

> Answer: LinkedIn

Question: Continuing with the previous scenario, you are trying to find the answer to the secret question: "Which school did you go to as a child?". What social media website would you consider checking to find the answer to such secret questions?

> Answer: Facebook

***

## Conclusion

This room provided an excellent overview of the core research tools every security professional needs. Building strong search skills is a foundational step that will constantly be utilized throughout my entire learning journey and career.
