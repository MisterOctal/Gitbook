---
icon: chart-network
---

# Networking Core Protocols

**Date:** 25.10.2024

**Room Category:** Walkthrough

**Core Concept:** Deep dive into the Application Layer (Layer 7) protocols that power the web, file transfers, and email communication.

This room is the third in our series, shifting focus from how data is moved (Routing, ARP, IP) to what that data actually represents. We explore the "human-facing" protocols of the internet. Understanding how these protocols function and how to interact with them manually via the command line is essential for identifying misconfigurations and intercepting clear-text credentials during security assessments.

***

## Task 1: Introduction

This room bridges the gap between basic connectivity and functional applications. We will look at how domain names are resolved, how websites are served, and the mechanics of sending and receiving digital mail.

Question: Get your notepad ready, and let’s begin.

> **Answer:** No answer needed

***

## Task 2: DNS: Remembering Addresses

DNS is the "phonebook of the internet," translating human-readable names like `google.com` into machine-readable IP addresses. It primarily uses **UDP port 53**.

**Key Record Types:**

* **A:** Maps to an IPv4 address.
* **AAAA:** Maps to an IPv6 address.
* **CNAME:** An alias (maps one domain to another).
* **MX:** Specifies the Mail Exchange server for the domain.

Question: Which DNS record type refers to IPv6?

> **Answer:** AAAA

Question: Which DNS record type refers to the email server?

> **Answer:** MX

***

## Task 3: WHOIS

WHOIS is a query and response protocol used for querying databases that store the registered users or assignees of an Internet resource, such as a domain name or an IP address block.

Question: When was the x.com record created? Provide the answer in YYYY-MM-DD format.

> **Answer:** 1993-04-02

Question: When was the twitter.com record created? Provide the answer in YYYY-MM-DD format.

> **Answer:** 2000-01-21

***

## Task 4: HTTP(S): Accessing the Web

HTTP (Port 80) and its secure counterpart HTTPS (Port 443) facilitate web browsing. By using `telnet` or `curl`, we can "speak" HTTP directly to a server.

**Manual HTTP Request Example:**

```http
telnet 10.49.138.217 80
GET /flag.html HTTP/1.1
Host: anything
```

Question: Use telnet to access the file flag.html on `10.49.138.217` . What is the hidden flag?

> **Answer:** THM{TELNET-HTTP}

***

## Task 5: FTP: Transferring Files

FTP (Port 21) is used for transferring files. While largely replaced by SFTP, it remains common in internal networks. It often supports "anonymous" login, which is a frequent finding in security audits.

<figure><img src="../.gitbook/assets/image (69).png" alt="" width="375"><figcaption></figcaption></figure>

**Common Commands:**

* `USER`: Specify username.
* `PASS`: Specify password.
* `RETR`: Retrieve (download) a file.
* `LIST`: List directory contents.

Question: Using the FTP client ftp on the AttackBox, access the FTP server at MACHINE\_IP and retrieve flag.txt. What is the flag found?

> **Answer:** THM{FAST-FTP}

***

## Task 6: SMTP: Sending Email

SMTP (Port 25) is the standard for **sending** emails. It is a text-based protocol where a client "handshakes" with the server before sending the message body.

<figure><img src="../.gitbook/assets/image (70).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Which SMTP command indicates that the client will start the contents of the email message?

> **Answer:** DATA

Question: What does the email client send to indicate that the email message has been fully entered?

> **Answer:** .

***

## Task 7: POP3: Receiving Email

POP3 (Port 110) is used to **retrieve** emails. Unlike IMAP, POP3 traditionally downloads the email and deletes it from the server, making it less ideal for multi-device synchronization.

<figure><img src="../.gitbook/assets/image (71).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Looking at the traffic exchange, what is the name of the POP3 server running on the remote server?

> **Answer:** Dovecot

Question: Use telnet to connect to MACHINE\_IP’s POP3 server. What is the flag contained in the fourth message?

> **Answer:** THM{TELNET\_RETR\_EMAIL}

***

## Task 8: IMAP: Synchronizing Email

IMAP (Port 143) is the modern standard for email retrieval, allowing for synchronization across multiple devices as the mail remains stored on the server.

<figure><img src="../.gitbook/assets/image (72).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What IMAP command retrieves the fourth email message?

> **Answer:** FETCH 4 body\[]

***

## Conclusion

We have now explored the primary application layer protocols. The move from simple text-based protocols (like HTTP and FTP) to secure, encrypted versions (HTTPS, SFTP) is a major theme in modern networking to prevent the kind of credential sniffing we observed in this room's Wireshark examples.

| Protocol | Default Port |
| -------- | ------------ |
| DNS      | 53           |
| HTTP     | 80           |
| HTTPS    | 443          |
| FTP      | 21           |
| SMTP     | 25           |
| POP3     | 110          |
| IMAP     | 143          |
