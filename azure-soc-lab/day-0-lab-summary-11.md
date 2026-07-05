---
icon: wrench
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 11 - Cowrie Modifications

**Date:** 05.07.2026

**Core Concept**: Honeypot Configuration for Post-Exploitation Telemetry

Day 11 focused on improving the quality of captured attack data. The initial honeypot configuration blocked all login attempts, providing visibility into attack volume but not attacker behavior after gaining access. To capture post-exploitation activity, the Cowrie userdb was updated with weak, easily guessable credentials.

***

## Section 1: Userdb Credential Configuration

The honeypot's userdb.txt file was updated to allow successful login attempts on commonly targeted accounts. Rather than blocking all access, the file now includes weak credentials that automated bots are likely to guess:

```yaml
root:x:123456
admin:x:admin123
test:x:123
user:x:user123
ubuntu:x:123456
deploy:x:deploy
user1:x:111111
claude:x:password
```

This configuration enables successful compromises, allowing bots to gain shell access and execute commands. The captured post-exploitation behavior (command execution, file access attempts, tool deployment) provides far richer telemetry than failed login attempts alone.

***

## Section 2: Rationale

The shift from a purely defensive honeypot (reject all logins) to a permissive one (accept specific weak credentials) reflects a deliberate trade-off:

Defense versus Intelligence: A real production system blocks attackers. A honeypot allows them in to learn their tactics.

By accepting logins on high-value accounts with simple passwords, the honeypot captures the full attack lifecycle: reconnaissance, exploitation, post-compromise activity, and lateral movement attempts. This data is far more valuable for understanding attacker behavior and developing effective detection rules.

***

## Conclusion

Day 11 is complete. Cowrie userdb is configured to accept weak credentials on commonly targeted accounts, enabling capture of post-exploitation behavior. If things go well and we get additional honeypot data, tomorrow we'll practice some elastic commands.
