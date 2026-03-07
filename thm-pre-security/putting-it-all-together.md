---
icon: server
---

# Putting it all together

Date: 02.03.2026

Room Category: Theory / Web

Core Concept: Combining DNS, HTTP, Web Servers, and Edge components.

This is the final room of the web fundamentals module! Tying all these concepts together is arguably the most important part of my studying. When I take my Security+ exam this November, they aren't just going to ask me about DNS in a vacuum; they'll want to know how a DNS request interacts with a Load Balancer or a WAF. For my future career as a SOC analyst, understanding this exact architecture is how I'll know exactly which logs to check when an attack occurs!

***

## Task 1: Putting it all together

This task is just a brief introduction to the room. We've learned about how a user requests a website, but enterprise networks have a lot of stuff sitting between the user and the actual web server. Time to learn about the "middlemen"!

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

***

## Task 2: Other Components

This task introduces the heavy lifters of a modern network:

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **Load Balancers:** When a site gets too much traffic, a load balancer distributes the requests across multiple servers. It uses "Health Checks" to make sure a server is actually alive before sending traffic to it.
* **CDNs (Content Delivery Networks):** These host static files (like images or CSS) on servers all around the world, drastically speeding up load times for users.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

* **WAFs (Web Application Firewalls):** This is a SOC analyst's best friend. It sits in front of the web server and inspects HTTP traffic to block common web attacks like XSS or SQL Injection.

Question: What can be used to host static files and speed up a clients visit to a website?

> Answer: CDN

Question: What does a load balancer perform to make sure a host is still alive?

> Answer: Health Check

Question: What can be used to help against the hacking of a website?

> Answer: WAF

***

## Task 3: How Web Servers Work

Web servers aren't just simple file hosts. Through the use of "Virtual Hosts", a single server (with a single IP address) can host hundreds of different websites by looking at the HTTP "Host" header. We also learned the difference between static content (always the same) and dynamic content (changes based on user input or database queries). Thankfully, the backend code processing that dynamic content is never visible to the client!

Question: What does web server software use to host multiple sites?

> Answer: Virtual Hosts

Question: What is the name for the type of content that can change?

> Answer: Dynamic

Question: Does the client see the backend code?

> Answer: Nay

***

## Task 4: Quiz

For this practical, we had to drag and drop the sequence of events into the correct order to simulate a full web request. The flow perfectly summarizes the module:

1. Request a URL from the browser
2. Local cache is checked for IP address
3. Recursive DNS server is checked for IP address
4. Root server is queried to find authoritative DNS server
5. Authoritative DNS server returns the IP address for the website
6. Request passes through a WAF
7. Request passes through a load balancer
8. Connect to the webserver on port 80/443
9. Web server receives GET request
10. Web application talks to database
11. HTML rendered by browser

Question: Drag and drop the tiles into the correct order of how a request to a website works to reveal the flag.

> Answer: THM{YOU\_GOT\_THE\_ORDER}

***

## Conclusion

That completes the Web Fundamentals module! Seeing the entire lifecycle of a web request from the moment you type a URL, through the DNS hierarchy, past the WAF, and into the backend database is incredibly satisfying. Having this solid baseline of how things are _supposed_ to work is going to make spotting malicious activity so much easier. I'm feeling really confident about the web-centric parts of my Security+ exam now. Time to move on to the next path and keep grinding before my HND ends!
