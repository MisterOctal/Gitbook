---
icon: key
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1728537810726.png
coverY: 0
coverHeight: 138
---

# Public Key Cryptography Basics

**Date:** 24.03.2026

**Room Category:** Walkthrough / Cyber Security 101

**Core Concept:** Exploring asymmetric cryptosystems (RSA, Diffie-Hellman) and their applications in securing remote access (SSH), web traffic (SSL/TLS), and email (PGP).

Cryptography lays the foundation for our digital world. While networking protocols have made it possible for devices spread across the globe to communicate, cryptography has made it possible to trust this communication. It ensures that data remains confidential, its integrity is maintained, and the sender's identity is verified. In this room, we move beyond simple shifts to explore how public and private keys allow us to establish secure connections over insecure channels.

***

## Task 1: Introduction

When communicating in the cyber realm, we face four major challenges:

1. **Authentication:** Confirming the identity of the person you are talking to.
2. **Authenticity:** Verifying that a message genuinely comes from the claimed source.
3. **Integrity:** Ensuring the data has not been altered during transit.
4. **Confidentiality:** Preventing unauthorized eavesdropping.

While symmetric encryption (private key) mainly protects confidentiality, asymmetric cryptography (public key) is the primary solution for authentication, authenticity, and integrity.

Question: Let's begin!

> **Answer:** No answer needed

***

## Task 2: Common Use of Asymmetric Encryption

Asymmetric encryption is significantly slower than symmetric encryption. Therefore, we rarely use it to encrypt large files. Instead, we use it to **negotiate a secret key** for symmetric encryption.

<figure><img src="../.gitbook/assets/image (80).png" alt="" width="375"><figcaption></figcaption></figure>

**The Analogy:** Imagine you want to send secret instructions to a friend. You ask your friend for a **lock** (Public Key) to which only they have the **key** (Private Key). You put your instructions in a box, snap their lock shut, and send it. Even if intercepted, only your friend can open the box.

Question: In the analogy presented, what real object is analogous to the public key?

> **Answer:** Lock

***

## Task 3: RSA (Rivest-Shamir-Adleman)

RSA is popular asymmetric encryption based on the mathematical difficulty of factoring the product of two very large prime numbers.

* **p and q:** Two large prime numbers.
* **n:** The product of p \* q.
* **e:** Public exponent.
* **d:** Private exponent.
* **Public Key:** (n, e)
* **Private Key:** (n, d)

**Calculation Walkthrough:**

1. n = p \* q
2. phi(n) = (p - 1) \* (q - 1)

Question: Knowing that p = 4391 and q = 6659. What is n?

> **Answer:** 29239669

Question: Knowing that p = 4391 and q = 6659. What is phi(n)?

> **Answer:** 29228620

***

## Task 4: Diffie-Hellman Key Exchange

Diffie-Hellman (DH) is not used for encryption but for **Key Exchange**. It allows two parties (Alice and Bob) to create a shared secret key over a public channel without ever actually sending the key itself.

**Math Flow:**

1. Alice and Bob agree on a prime p and generator g.
2. Alice picks secret a, Bob picks secret b.
3. Alice sends A = g<sup>a</sup> mod p. Bob sends B = g<sup>b</sup> mod p.
4. Shared Secret s = B<sup>a</sup> mod p (or A<sup>b</sup> mod p).

Question: Consider p = 29, g = 5, a = 12. What is A?

> **Answer:** 7

Question: Consider p = 29, g = 5, b = 17. What is B?

> **Answer:** 9

Question: Knowing that p = 29, a = 12, and you have B from the second question, what is the key calculated by Bob?

> **Answer:** 24

Question: Knowing that p = 29, b = 17, and you have A from the first question, what is the key calculated by Alice?

> **Answer:** 24

***

## Task 5: SSH

SSH (Secure Shell) uses asymmetric encryption to authenticate users. Instead of a password, you can use a **Key Pair**.

* **Public Key:** Stored on the server in `~/.ssh/authorized_keys`.
* **Private Key:** Stored on your local machine (e.g., `id_rsa`).
* **Permissions:** Your private key must be protected (chmod 600).

Question: Check the SSH Private Key in `~/Public-Crypto-Basics/Task-5`. What algorithm does the key use?

> **Answer:** RSA

***

## Task 6: Digital Signatures and Certificates

Digital signatures prove who sent a message. Certificates prove who owns a website.

* **TLS Certificates:** Used by web servers to prove their identity to your browser.
* **Certificate Authority (CA):** A trusted third party (like **Let's Encrypt**) that validates certificates.

Question: What does a remote web server use to prove itself to the client?

> **Answer:** Certificate

Question: What would you use to get a free TLS certificate for your website?

> **Answer:** Let's Encrypt

***

## Task 7: PGP and GPG

**PGP (Pretty Good Privacy)** and its open-source version **GPG (GnuPG)** are used to sign and encrypt emails and files.

* `gpg --import`: Adds a key to your keyring.
* `gpg --decrypt`: Uses your private key to read a message encrypted with your public key.

Question: Use GPG to decrypt the message in `~/Public-Crypto-Basics/Task-7`. What secret word does the message hold?

> **Answer:** Pineapple

***

## Conclusion

Public key cryptography solves the "Key Distribution Problem" of symmetric encryption. By using mathematically linked key pairs, we can verify identities (Signatures), secure web traffic (Certificates), and remotely manage servers (SSH) without ever revealing our private secrets.
