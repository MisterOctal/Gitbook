---
icon: server
---

# HTTP in Detail

Date: 02.03.2026

Room Category: Theory

Core Concept: How web clients and servers communicate using HTTP/HTTPS.

As someone whose turning 17 soon, getting ahead on these web fundamentals before my HND finishes up in October is going to put me way ahead of the curve. By the time I get to University College Birmingham in 2027, I'll know this stuff like the back of my hand. HTTP is the backbone of the web, and understanding requests, responses, headers, and status codes is absolute gold for log analysis and acing that Security+ exam on November 13th!

***

## Task 1: What is HTTP(S)?

HTTP is a plaintext protocol used to transfer web pages, meaning anyone sniffing the network can read the traffic. HTTPS adds a layer of TLS/SSL encryption. From a SOC perspective, this is a double-edged sword: it protects user data, but it also hides malicious payloads unless the organization uses SSL decryption.

Question: What does HTTP stand for?

> Answer: HyperText Transfer Protocol

Question: What does the S in HTTPS stand for?

> Answer: Secure

Question: On the mock webpage on the right there is an issue, once you've found it, click on it. What is the challenge flag?

> Answer: THM{INVALID\_HTTP\_CERT}

***

## Task 2: Requests and Responses

We've all used URLs (Uniform Resource Locators) before. When we try to access a website we always encounter them at the top of our browser.

<figure><img src="../.gitbook/assets/image (5).png" alt="" width="563"><figcaption></figcaption></figure>

Every interaction on the web is a conversation. Your browser sends a "Request" (saying "give me this page"), and the server replies with a "Response" (which includes status codes and the actual HTML). Knowing how to manually read these raw requests is crucial for finding anomalies.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What HTTP protocol is being used in the above example?

> Answer: HTTP/1.1

Question: What response header tells the browser how much data to expect?

> Answer: Content-Length

***

## Task 3: HTTP Methods

Methods tell the server _what_ we want to do. GET is for reading, POST is for creating/sending data, PUT is for updating, and DELETE is... well, for deleting. Attackers love using POST requests for data exfiltration or Command and Control (C2) beaconing, so keeping an eye on abnormal POST traffic is a classic SOC analyst task.

Question: What method would be used to create a new user account?

> Answer: POST

Question: What method would be used to update your email address?

> Answer: PUT

Question: What method would be used to remove a picture you've uploaded to your account?

> Answer: DELETE

Question: What method would be used to view a news article?

> Answer: GET

***

## Task 4: HTTP Status Codes

Status codes are grouped into blocks. 2xx means success, 3xx means redirect, 4xx means the client messed up, and 5xx means the server messed up. A massive flood of 404 (Not Found) errors in the logs usually indicates a directory enumeration attack (like when we used `dirb` in the Offensive Security room!). A flood of 500 errors could indicate a successful Denial of Service or an application crashing from a SQL injection.

Question: What response code might you receive if you've created a new user or blog post article?

> Answer: 201

Question: What response code might you receive if you've tried to access a page that doesn't exist?

> Answer: 404

Question: What response code might you receive if the web server cannot access its database and the application crashes?

> Answer: 503

Question: What response code might you receive if you try to edit your profile without logging in first?

> Answer: 401

***

## Task 5: Headers

Headers are extra metadata sent with requests and responses. As a defender, the `User-Agent` header is incredibly important. If I see a request coming from a User-Agent that says `curl/7.68.0` or `python-requests/2.25.1` trying to access a login page, that's highly suspicious compared to a normal browser like Chrome or Firefox.

Question: What header tells the web server what browser is being used?

> Answer: User-Agent

Question: What header tells the browser what type of data is being returned?

> Answer: Content-Type

Question: What header tells the web server which website is being requested?

> Answer: Host

***

## Task 6: Cookies

Because HTTP is "stateless" (it forgets who you are immediately after every request), cookies are used to maintain sessions and keep you logged in. If an attacker steals your session cookie via Cross-Site Scripting (XSS), they can completely bypass the login screen.

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Which header is used to save cookies to your computer?

> Answer: Set-Cookie

***

## Task 7: Making Requests

This task is a great practical exercise using the in-browser emulator to manipulate parameters and methods to retrieve flags!

Question: Make a GET request to /room page.

> Answer: THM{YOU'RE\_IN\_THE\_ROOM}

Question: Make a GET request to /blog page and set the id parameter to 1.

> Answer: THM{YOU\_FOUND\_THE\_BLOG}

Question: Make a DELETE request to /user/1 page.

> Answer: THM{USER\_IS\_DELETED}

Question: Make a PUT request to /user/2 page with the username parameter set to admin.

> Answer: THM{USER\_HAS\_UPDATED}

Question: Make a POST request to /login page with the username of thm and a password of letmein.

> Answer: THM{HTTP\_REQUEST\_MASTER}

***

## Conclusion

That concludes the HTTP in Detail room! Going from the high-level OSI model down to the actual nuts and bolts of HTTP methods, headers, and status codes makes everything click into place. Being able to read raw HTTP traffic is a mandatory skill for threat hunting and incident response.
