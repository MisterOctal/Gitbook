---
icon: globe
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/66c513e4445cb5649e636a36/room-content/66c513e4445cb5649e636a36-1725441193460.png
coverY: 0
coverHeight: 138
---

# Web Application Basics

**Date:** 28.03.2026&#x20;

**Room Category:** Walkthrough

**Core Concept:** Understanding the "glue" of the internet—how URLs, HTTP requests, and server responses interact to deliver web content.

Have you ever wondered what actually happens when you type a website name into your browser and hit enter? This room breaks down the invisible handshake between your computer and a remote server. Mastering these fundamentals is critical for security because almost every modern attack surface, from SQL injection to broken authentication all rely  on manipulating these very protocols.

***

## Task 1: Introduction

This room covers the essential components that make the web function: URLs, the structure of HTTP messages, and how security headers protect users.

* **Objective:** Understand Front End vs. Back End, URL components, HTTP methods, and Security Headers.

Question: I am ready to learn about Web Applications!

> **Answer:** No answer needed

***

## Task 2: Web Application Overview

To understand web apps, we can use the **Planet Analogy**:

<figure><img src="../.gitbook/assets/image (83).png" alt="" width="188"><figcaption></figcaption></figure>

* **Front End (The Surface):** This is what the user sees. Built with HTML (Structure), CSS (Style), and JavaScript (Logic/Interactivity).

<figure><img src="../.gitbook/assets/image (84).png" alt="" width="188"><figcaption></figcaption></figure>

* **Back End (Sub-surface):** The "engine room." Includes the Database (Storage), Infrastructure (Servers/Networking), and a WAF (Web Application Firewall) which acts as the protective atmosphere filtering incoming traffic.

Question: Which component on a computer is responsible for hosting and delivering content?

> **Answer:** Web Server

Question: Which tool is used to access and interact with web applications?

> **Answer:** Web Browser

Question: Which component acts as a protective layer, filtering incoming traffic?

> **Answer:** Web Application Firewall

***

## Task 3: Uniform Resource Locator (URL)

<figure><img src="../.gitbook/assets/image (85).png" alt="" width="563"><figcaption></figcaption></figure>

A URL is a roadmap to a specific resource on the internet. It consists of several key parts:

* **Scheme:** The protocol (e.g., `https://`).
* **Host:** The domain name or IP address.
* **Port:** The specific gate (Default 80 for HTTP, 443 for HTTPS).
* **Path:** The specific file or resource location.
* **Query String:** Additional data passed via `?`.
* **Fragment:** A specific section of a page identified by `#`.

Question: Which protocol provides encrypted communication?

> **Answer:** HTTPS

Question: What term describes registering misspelled variations of popular domains?

> **Answer:** Typosquatting

Question: What part of a URL is used to pass additional information like search terms?

> **Answer:** Query String

***

## Task 4: HTTP Messages

Communication happens via messages consisting of a **Start Line**, **Headers**, an **Empty Line** (the crucial divider), and an optional **Body** (the payload).

<figure><img src="../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

Question: Which HTTP message is returned by the web server after processing a client's request?

> **Answer:** HTTP Response

Question: What follows the headers in an HTTP message?

> **Answer:** Empty Line

***

## Task 5: HTTP Request: Request Line and Methods

<figure><img src="../.gitbook/assets/image (87).png" alt="" width="375"><figcaption></figcaption></figure>

HTTP Methods define the "verb" or the action you want to take:

* **GET:** Retrieve data from the server.
* **POST:** Submit data to the server (e.g., login forms).
* **PUT/PATCH:** Update existing data.
* **DELETE:** Remove a resource.
* **OPTIONS:** Ask the server which methods it supports.

Question: Which version introduced persistent connections and remains widely used?

> **Answer:** HTTP/1.1

Question: Which method describes the communication options for the target resource?

> **Answer:** OPTIONS

Question: Which component specifies the specific resource or endpoint on the server?

> **Answer:** URL Path

***

## Task 6: Request Headers and Body

Headers provide essential metadata (Host, User-Agent, Cookie). The body contains the actual data being sent, often formatted as JSON or URL-encoded text.

#### Request Body Formats

```http
# URL Encoded (Default for standard HTML forms)
Content-Type: application/x-www-form-urlencoded
name=Aleksandra&age=27

# JSON (Standard for modern APIs)
Content-Type: application/json
{ "name": "Aleksandra", "age": 27 }
```

Question: Which header specifies the domain name of the web server?

> **Answer:** Host

Question: What is the default content type for form submissions?

> **Answer:** application/x-www-form-urlencoded

Question: Which part of an HTTP request contains info like host, user agent, and content type?

> **Answer:** Headers

***

## Task 7: HTTP Response: Status Codes

<figure><img src="../.gitbook/assets/image (88).png" alt="" width="563"><figcaption></figcaption></figure>

Status codes are the server's way of telling the browser the outcome:

* **1xx (Informational):** Request received, continuing process.
* **2xx (Success):** The action was successfully received and accepted (e.g., 200 OK).
* **3xx (Redirection):** Further action is needed to complete the request (e.g., 301 Moved).
* **4xx (Client Error):** The request contains bad syntax or cannot be fulfilled (e.g., 404 Not Found).
* **5xx (Server Error):** The server failed to fulfill an apparently valid request (e.g., 500 Internal Error).

Question: What part of a response provides the version, code, and reason phrase?

> **Answer:** Status Line

Question: Which category indicates the server encountered an internal issue?

> **Answer:** Server Error Responses (500-599)

Question: Which status code indicates a resource could not be found?

> **Answer:** 404

***

## Task 8: Response Headers and Body

<figure><img src="../.gitbook/assets/image (89).png" alt="" width="375"><figcaption></figcaption></figure>

* **Set-Cookie:** Instructs the browser to store a session token.
* **Cache-Control:** Defines how long the browser should store the page locally.
* **Location:** Used in 3xx responses to tell the browser where to go next.

Question: Which HTTP response header can reveal information about the web server's software and version, potentially exposing it to security risks if not removed?

> **Answer:** Server

Question: Which flag should be added to cookies in the Set-Cookie HTTP response header to ensure they are only transmitted over HTTPS, protecting them from being exposed during unencrypted transmissions?

> **Answer:** Secure

Question: Which flag should be added to cookies in the Set-Cookie HTTP response header to prevent them from being accessed via JavaScript, thereby enhancing security against XSS attacks?

> **Answer:** HttpOnly

***

## Task 9: Security Headers

These headers are the "armor" for the web application, hardening the browser against common attacks:

* **CSP (Content Security Policy):** Prevents XSS by defining which scripts can run.
* **HSTS (HTTP Strict Transport Security):** Forces the browser to use HTTPS only.
* **X-Content-Type-Options:** Set to `nosniff` to prevent the browser from guessing file types.

Question: Which CSP property defines where scripts can be loaded from?

> **Answer:** script-src

Question: Which HSTS directive applies the policy to subdomains?

> **Answer:** includeSubDomains

Question: Which directive prevents browsers from guessing MIME types?

> **Answer:** nosniff

***

## Task 10: Practical Task

This task is pretty simple, we just interact with the static site and send HTTP requests for flags.

Question: Make a GET request to `/api/users`. What is the flag?

> **Answer:** THM{YOU\_HAVE\_JUST\_FOUND\_THE\_USER\_LIST}

Question: Make a POST request to `/api/user/2` (Update Bob's country to US).

> **Answer:** THM{YOU\_HAVE\_MODIFIED\_THE\_USER\_DATA}

Question: Make a DELETE request to `/api/user/1`.

> **Answer:** THM{YOU\_HAVE\_JUST\_DELETED\_A\_USER}

***

## Conclusion

<figure><img src="../.gitbook/assets/image (90).png" alt="" width="375"><figcaption></figcaption></figure>

Completing this room has clarified how much is happening "under the hood" during a simple web search. Understanding the standard HTTP handshake, from the initial request methods to the status codes returned by the server is fundamental for any web-based investigation. I now have a much stronger grasp on how data is formatted in the body and how security headers act as a critical line of defense against modern web vulnerabilities.
