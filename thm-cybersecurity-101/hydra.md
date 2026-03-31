---
icon: hydra
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1729235938626.png
coverY: 0
coverHeight: 138
---

# Hydra

**Date:** 31.03.2026

**Room Category:** Walkthrough

**Core Concept:** Using Hydra, a high-speed network login cracker, to perform online brute-force attacks against various protocols.

Hydra is one of the most famous tools in a penetration tester's arsenal. While tools like Hashcat or John the Ripper are used for _offline_ cracking (cracking a stolen database of hashes), Hydra is designed for _online_ cracking. It interacts directly with a service like a website login or an SSH port trying thousands of password combinations in real-time to find a valid set of credentials.

***

## Task 1: Hydra Introduction

Hydra is a "parallelized login cracker" which supports a massive list of protocols, including SSH, FTP, HTTP, HTTPS, SMB, and many more. The power of Hydra lies in its speed and its ability to handle multiple "threads" or connections simultaneously. This makes it much faster than a human manually guessing passwords. However, it is a "noisy" tool; every failed attempt is logged by the server, meaning it is easily detected by modern security monitoring systems (SIEMs).

Question: Read the above and have Hydra at the ready.

> **Answer:** No answer needed

***

## Task 2: Using Hydra

To use Hydra effectively, you need three things: a target IP, a known (or guessed) username, and a wordlist (like the famous `rockyou.txt`). The syntax changes depending on whether you are attacking a simple service like SSH or a complex web form.

When attacking web forms, you must identify the "failure string"—the message the website displays when a login fails (e.g., "Invalid credentials"). Hydra uses this string to determine that a password was wrong; if that string _doesn't_ appear in the response, Hydra assumes it has found the correct password.

#### Flag 1: Web Brute Force

To find the web flag, we targeted the `/login` endpoint. We used the `-l` flag for the username "molly" and the `http-post-form` module. The module requires the path, the form parameters (where `^USER^` and `^PASS^` act as placeholders), and the failure message.

**Command Used:**

```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.112.184.177 http-post-form "/login:username=^USER^&password=^PASS^:Your username or password is incorrect." -V -t 4
```

Question: Use Hydra to brute-force molly's web password. What is the value of flag 1?

> **Answer:** THM{2673a7dd116de68e85c48ec0b1f2612e}

#### Flag 2: SSH Brute Force

Once we had the web password, we moved to the SSH service. SSH is generally more secure and may have rate-limiting, so we kept our threads (`-t`) at a reasonable level.

**Command Used:**

```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.112.184.177 -t 4 ssh -V
```

Question: Use Hydra to brute-force molly's SSH password. What is the value of flag 2?

> **Answer:** THM{c8eeb0468febbadea859baeb33b2541b}

***

## Conclusion

Hydra is a powerful reminder of why strong password policies and multi-factor authentication (MFA) are critical. Even with a fast tool, a long and complex password could take centuries to crack. As a security professional, always ensure that services like SSH are not exposed to the public internet without protection like Fail2Ban or key-based authentication to mitigate the risk of a Hydra-style attack.
