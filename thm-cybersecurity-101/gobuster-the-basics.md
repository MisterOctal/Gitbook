---
icon: globe
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1729235938626.png
coverY: 0
coverHeight: 138
---

# Gobuster: The Basics

**Date:** 31.03.2026

**Room Category:** Walkthrough

**Core Concept:** Introduction to Gobuster, a fast, Go-based offensive security tool used to enumerate web directories, subdomains, and virtual hosts.

Gobuster is a staple in penetration testing and bug bounty hunting. Operating between the reconnaissance and scanning phases, it uses brute-force techniques combined with specific wordlists to uncover hidden resources on web servers that are not easily accessible through standard navigation.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (9).png" alt="" width="188"><figcaption></figcaption></figure>

This room focuses on the offensive security tool Gobuster, often used for reconnaissance. We will explore how this tool can enumerate web directories, subdomains, and virtual hosts. The room follows a hands-on approach to understand enumeration and brute-forcing using wordlists.

Question: I'm ready to learn about Gobuster!

> **Answer:** No answer needed

***

## Task 2: Environment and Setup

The room utilizes an Ubuntu 20.04 VM acting as a web server that hosts multiple subdomains and vhosts, along with WordPress and Joomla CMS installations.

To ensure the AttackBox can properly resolve the domains used in this room, a local DNS configuration change is required. This involves editing `/etc/resolv-dnsmasq` to include the target IP (`10.112.172.81`) as the primary nameserver and restarting the `dnsmasq` service.

Question: I assigned the 10.112.172.81 as the first nameserver in the /etc/resolv-dnsmasq file and restarted the Dnsmasq service.

> **Answer:** No answer needed

***

## Task 3: Gobuster: Introduction

Gobuster relies on wordlists to brute-force possibilities. It supports several enumeration modes, including `dir` (directories/files), `dns` (subdomains), `vhost` (virtual hosts), and cloud buckets (`s3`, `gcs`).

Performance can be tuned using global flags, most notably `-t` for threads (default is 10) and `-w` to specify the wordlist path.

Question: What flag do we use to specify the target URL?

> **Answer:** -u

Question: What command do we use for the subdomain enumeration mode?

> **Answer:** dns

***

## Task 4: Use Case: Directory and File Enumeration

The `dir` mode is used to discover hidden directories and files. It's common to look for standard CMS paths (like `/wp-admin/`) or exposed backups. You can narrow down file searches using the `-x` flag to specify extensions (e.g., `-x .php,.js,.txt`).

**Example Command:** `gobuster dir -u "http://www.offensivetools.thm" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r -x .php,.js`

Question: Which flag do we have to add to our command to skip the TLS verification? Enter the long flag notation.

> **Answer:** --no-tls-validation

Question: Enumerate the directories of www.offensivetools.thm. Which directory catches your attention?

> **Answer:** secret

Question: Continue enumerating the directory found in question 2. You will find an interesting file there with a .js extension. What is the flag found in this file?

> **Answer:** THM{ReconWasASuccess}

***

## Task 5: Use Case: Subdomain Enumeration

The `dns` mode brute-forces subdomains by making direct DNS queries rather than web requests. This is crucial for finding development servers or unpatched administrative portals tied to the main domain.

**Example Command:** `gobuster dns -d offensivetools.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt`

Question: Apart from the dns keyword and the -w flag, which shorthand flag is required for the command to work?

> **Answer:** -d

Question: Use the commands learned in this task, how many subdomains are configured for the offensivetools.thm domain?

> **Answer:** 4

***

## Task 6: Use Case: Vhost Enumeration

Virtual hosts (`vhost`) operate on the same IP address but serve different websites based on the `Host` header in the HTTP request. Unlike `dns` mode (which checks DNS records), `vhost` mode actively sends HTTP requests and manipulates the `Host` header to see if the server responds differently.

Because many non-existent vhosts might return default pages (False Positives), filtering using `--exclude-length` is vital to isolate genuine 200 OK responses.

**Example Command:** `gobuster vhost -u "http://10.112.172.81" --domain offensivetools.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 250-320`

Question: Use the commands learned in this task to answer the following question: How many vhosts on the offensivetools.thm domain reply with a status code 200?

> **Answer:** 4

***

## Task 7: Conclusion

Gobuster is a versatile and essential tool for web enumeration. Mastering the differences between directory path brute-forcing (`dir`), DNS resolution checking (`dns`), and HTTP Host header manipulation (`vhost`) ensures comprehensive coverage during the reconnaissance phase of a penetration test.

#### Command & Flag Summary

<table data-header-hidden><thead><tr><th width="138.5999755859375"></th><th width="212.79998779296875"></th><th></th></tr></thead><tbody><tr><td>Category</td><td>Command/Flag</td><td>Description</td></tr><tr><td><strong>Modes</strong></td><td><code>dir</code></td><td>Classic directory and file enumeration</td></tr><tr><td></td><td><code>dns</code></td><td>DNS subdomain enumeration</td></tr><tr><td></td><td><code>vhost</code></td><td>Virtual host enumeration (via HTTP headers)</td></tr><tr><td><strong>Global Flags</strong></td><td><code>-u</code></td><td>The target URL (required for <code>dir</code> and <code>vhost</code>)</td></tr><tr><td></td><td><code>-w</code></td><td>Path to the wordlist</td></tr><tr><td></td><td><code>-t</code></td><td>Number of concurrent threads (default: 10)</td></tr><tr><td><strong>Dir Flags</strong></td><td><code>-x</code></td><td>File extensions to search for (e.g., <code>.php,.txt</code>)</td></tr><tr><td></td><td><code>-r</code></td><td>Follow redirects</td></tr><tr><td></td><td><code>--no-tls-validation</code></td><td>Skip TLS certificate verification</td></tr><tr><td><strong>DNS Flags</strong></td><td><code>-d</code></td><td>The target domain name</td></tr><tr><td><strong>Vhost Flags</strong></td><td><code>--domain</code></td><td>The domain name to append to wordlist entries</td></tr><tr><td></td><td><code>--append-domain</code></td><td>Appends main domain to words from wordlist</td></tr><tr><td></td><td><code>--exclude-length</code></td><td>Filter out responses with specific content lengths</td></tr></tbody></table>
