---
icon: user-hoodie
---

# Become a Hacker

Date: 16.03.2026

Room Category: Walkthrough

Core Concept: An introduction to offensive security, common hacking terminology, and basic exploitation techniques.

Exploring the offensive side of cybersecurity helps build a stronger understanding of how attacks actually happen in the wild. Learning to think like an attacker and systematically uncover vulnerabilities is a practical skill that translates directly into better defensive strategies.

***

### Task 1: What Is Offensive Security?

Offensive security involves proactively testing systems by attempting to break into them. The goal is to identify and fix weaknesses before malicious actors can exploit them. This approach is highly structured, legal, and ethical, focusing on asking questions like what is exposed and how a system behaves under unexpected inputs.

Question: I understand the learning objectives and am ready to learn about Offensive Security!

> Answer: No answer needed

***

### Task 2: Finding Weaknesses

Before launching an attack, it is important to understand key terminology like vulnerabilities, exploits, and engagement scope. In this task, we interacted with a simulated web application to uncover hidden pages that the developer accidentally left publicly accessible. By using a directory enumeration tool similar to GoBuster, we successfully discovered a hidden administration portal.

The following command was used to enumerate the directories on the site:

```bash
gobuster dir --url http://www.onlineshop.thm/ -w /usr/share/wordlists/dirbuster/directory-list.txt
```

Question: What is the name of the hidden page that you found?

> Answer: /login

Question: What HTTP status code is returned when accessing the hidden page?

> Answer: 200

***

### Task 3: Exploiting Weaknesses

Finding a hidden login page is just the first domino falling. Offensive security is all about chaining small weaknesses together to create a larger impact. Since the login page was exposed, the next logical step was assuming the developer might have also used a weak password. By performing a dictionary attack using a brute force tool called Hydra, we successfully cracked the administrator's credentials.

The following command was used to brute-force the possible passwords:

```bash
hydra -l admin -P passlist.txt www.onlineshop.thm http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V
```

Question: Using either manual testing or an automated dictionary attack, what password was discovered for the admin account?

> Answer: qwerty

Question: After logging in using the password found, what secret message is displayed on the page?

> Answer: THM{born\_to\_hack!}

Question: Review the output of the Hydra dictionary attack. How many password attempts were made before the correct password was found?

> Answer: 17

***

### Conclusion

This room provided a fantastic, hands on introduction to the hacker mindset. From enumerating hidden directories to brute forcing a login page, chaining these basic vulnerabilities together demonstrates exactly how real world breaches often begin. Understanding the offensive methodology is a great way to improve your defensive awareness.
