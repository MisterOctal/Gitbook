---
icon: hat-chef
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1729043928499.png
coverY: 0
coverHeight: 138
---

# CyberChef: The Basics

**Date:** 03.04.2026

**Room Category:** Walkthrough

**Core Concept:** Using CyberChef, the "Swiss Army Knife" for data operations, to perform encoding, decoding, data extraction, and timestamp conversions.

CyberChef is a web-based application used for various cyber operations. It uses a "Recipe" system where multiple "Operations" are linked together to process "Input" into "Output." It is an essential tool for analysts who need to quickly manipulate data without writing custom scripts.

***

## Task 1: Introduction

CyberChef handles tasks ranging from simple XOR/Base64 encoding to complex AES encryption. It is highly intuitive and runs entirely in the web browser, meaning no data leaves your local environment if used offline. This makes it a safe and versatile "digital kitchen" for handling sensitive strings or malicious code fragments.

<figure><img src="../.gitbook/assets/image (12) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Proceed with the next tasks to learn more!

> **Answer:** No answer needed

***

## Task 2: Accessing the Tool

CyberChef is designed for maximum accessibility, ensuring you can use it regardless of your network environment. There are two primary ways to run the tool:

1. **Online Access:** You can use the official hosted version by navigating to the CyberChef website in any modern browser. This is the fastest way to get started.
2. **Offline/Local Copy:** For sensitive work where you cannot risk data leaving your machine, you can download a standalone HTML file from the GitHub repository. This allows CyberChef to run entirely locally without an internet connection.

Question: I have access to CyberChef and I’m ready to dive into it.

> **Answer:** No answer needed

***

## Task 3: Navigating the Interface

The CyberChef interface is divided into four main functional areas, each serving a specific purpose in the "cooking" process:

* **Operations Area:** This is the library of all available tools. You can search for specific functions like "From Base64" or "To Hex" here.
* **Recipe Area:** Considered the **heart of the tool**, this is where you drag and drop operations to build a sequence. You can adjust arguments for each "ingredient" here.
* **Input Area:** This is where you provide the raw data. You can type, paste text, or even upload files and folders directly.
* **Output Area:** Once the "Bake" button is pressed (or if Auto Bake is on), the results of your operations appear here. You can then copy the result or save it as a file.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: In which area can you find "From Base64"?

> **Answer:** operations

Question: Which area is considered the heart of the tool?

> **Answer:** Recipe

***

## Task 4: The CyberChef Thought Process

Before processing data, it is important to follow a structured four-step workflow to ensure accuracy and efficiency:

1. **Objective:** Define what you want to achieve. For instance, are you trying to find a hidden flag or just clean up a messy log file?
2. **Input:** Paste or upload your raw data into the input area. You can also drag and drop files directly into the interface.
3. **Operations:** Select the tools needed. If the data looks like random text, you might try categories like "Encryption/Encoding" (Base64, ROT13, etc.).
4. **Output:** Check if the result is what you intended. If the output is still gibberish, you may need to add more "ingredients" to your recipe or change the order of operations.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Question: At which step would you determine, "What do I want to accomplish?"

> **Answer:** 1

***

## Task 5: Practice, Practice, Practice

This task explores the most common categories used in security investigations. Mastering these allow an analyst to pivot quickly during an incident.

## Extractors

Useful for pulling specific patterns out of a large block of messy text or unstructured data:

* **Extract IP addresses:** Finds IPv4 and IPv6 addresses, which is crucial for identifying C2 servers.
* **Extract URLs:** Finds web addresses (requires protocol like HTTP/FTP) to help identify phishing domains.
* **Extract email addresses:** Finds strings in the `user@domain.com` format to map out potential targets or senders.

## Date and Time

* **UNIX Timestamp:** A 32-bit value representing seconds since January 1, 1970. It is the standard format for many system logs.
* **From UNIX Timestamp:** Converts the number into a readable date string so an analyst can establish a timeline of events.
* **To UNIX Timestamp:** Converts a date string into the 10-digit numeric format used for database queries.

## Data Format (Encodings)

* **Base64:** Transforms binary data into ASCII text, commonly used in email attachments and malware obfuscation.
* **Base58/Base62/Base85:** Alternative notations often used in specific applications like Bitcoin or Adobe PDF to improve readability or efficiency.
* **URL Decode:** Converts percent-encoded characters (like `%2F`) back to raw symbols (like `/`), which is helpful when analyzing suspicious web traffic.

## Manual Base64 Conversion Example ("THM")

Understanding the manual process helps you recognize Base64 patterns even without tools:

1. **Convert to Binary:** T(84), H(72), M(77) $\rightarrow$ `01010100`, `01001000`, `01001101`.
2. **Divide into 6-bit chunks:** `010101` (21), `000100` (4), `100001` (33), `001101` (13).
3. **Map to Base64 Index:** 21=**V**, 4=**E**, 33=**h**, 13=**N**. Result: `VEhN`.

Question: What is the hidden email address?

> **Answer:** hidden@hotmail.com

Question: What is the hidden IP address that ends in .232?

> **Answer:** 102.20.11.232

Question: Which domain address starts with the letter "T"?

> **Answer:** TryHackMe.com

Question: What is the binary value of the decimal number 78?

> **Answer:** 01001110

Question: What is the URL encoded value of https://tryhackme.com/r/careers?

> **Answer:** https%3A%2F%2Ftryhackme%2Ecom%2Fr%2Fcareers

***

## Task 6: Your First Official Cook

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

In this stage, we apply our knowledge to real-world data processing challenges, combining multiple operations to reveal hidden information.

Question: Using the file you downloaded in Task 5, which IP starts and ends with "10"?

> **Answer:** 10.10.2.10

Question: What is the base64 encoded value of the string "Nice Room!"?

> **Answer:** TmljZSBSb29tIQ==

Question: What is the URL decoded value for https%3A%2F%2Ftryhackme%2Ecom%2Fr%2Froom%2Fcyberchefbasics?

> **Answer:** https://tryhackme.com/r/room/cyberchefbasics

Question: What is the datetime string for the Unix timestamp 1725151258?

> **Answer:** Sun 1 September 2024 00:40:58 UTC

Question: What is the Base85 decoded string of the value <+oue+DGm>Ap%u7?

> **Answer:** This is fun!

***

## Conclusion

CyberChef is a vital tool for security analysts and researchers alike. It simplifies complex data manipulation, allowing you to quickly pivot from encoded strings to actionable intelligence like IPs, URLs, or cleartext messages. Whether you are performing malware analysis or simply cleaning up logs, CyberChef is an indispensable part of the security toolkit.
