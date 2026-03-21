---
icon: shield
---

# Operating System Security

**Date:** 08.03.2026

**Room Category:** Walkthrough

**Core Concept:** Principles of OS security, the CIA triad, and practical password/privilege escalation.

This room is a solid follow up to the basics. As I work toward finishing my HND this October, understanding how an attacker moves from a low privileged user to root is vital. In a SOC environment, I will be looking for exactly these kinds of patterns: unauthorized SSH logins, suspicious use of the switch user command, and attempts to access sensitive system files.

***

## Task 1: Introduction to Operating System Security

Operating systems are the brains of our devices, sitting between the hardware and the applications. Because they handle so much sensitive data (banking apps, private photos, passwords), they are the primary target for attackers. This task introduces the CIA triad: Confidentiality, Integrity, and Availability. Every security measure we implement is designed to protect one or more of these three pillars.

Question: Which of the following is not an operating system?

> **Answer:** Thunderbird

***

## Task 2: Common Examples of Security

We looked at the most common weaknesses that lead to compromise: weak authentication, poor file permissions, and malicious software. A huge takeaway here is the danger of weak passwords. Many people still use predictable patterns like "123456" or "qwerty" which are easily cracked using dictionary attacks. We also touched on the Principle of Least Privilege, which states that a user should only have access to the specific data they need to perform their job.

<figure><img src="../.gitbook/assets/image (29).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Which of the following is a strong password?

* iloveyou
* 1q2w3e4r5t
* LearnM00r
* qwertyuiop

> **Answer:** LearnM00r

***

## Task 3: Practical Example: Security in Action

This was the hands on part of the room. We acted as a security auditor investigating a compromised system. We found a username and password on a sticky note (a classic physical security fail) and used them to gain entry via SSH. Once inside, we performed basic reconnaissance, moved laterally to another user, and eventually escalated to root by finding a password accidentally left in the command history.

Below are the commands used during the investigation:

```
# Connect to the remote machine via SSH
ssh sammy@<target_ip>

# Identify the current logged in user
whoami

# List files and check for hidden ones
ls -la

# Read file contents (checking for notes or passwords)
cat draft.txt

# Switch to another user (John) using a discovered password
su - johnny

# Check the command history for mistakes
history

# Switch to the root user using the password found in history
su - root

# Read the final flag
cat /root/flag.txt
```

Question: Based on the top 7 passwords, let’s try to find Johnny’s password. What is the password for the user `johnny`?

> **Answer:** abc123

Question: Once you are logged in as Johnny, use the command `history` to check the commands that Johnny has typed. We expect Johnny to have mistakenly typed the `root` password instead of a command. What is the root password?

> **Answer:** happyHack!NG

Question: While logged in as Johnny, use the command `su - root` to switch to the `root` account. Display the contents of the file `flag.txt` in the `root` directory. What is the content of the file?

> **Answer:** THM{YouGotRoot}

***

## Conclusion

That wraps up Operating System Security! It's a clear reminder that technical security is only as strong as the people using it. Finding a root password in a user's command history is a common occurrence in real world assessments. Understanding these "human" vulnerabilities is going to be a huge part of my work as an analyst. It's great to see these theory concepts applied in a practical scenario.
