---
icon: key
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1728537810726.png
coverY: 0
coverHeight: 138
---

# Cryptography Basics

**Date:** 24.03.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the foundation of digital trust through encryption, historical ciphers, and the fundamental mathematics of modern security.

Cryptography lays the foundation for our digital world. While networking protocols have made it possible for devices spread across the globe to communicate, cryptography has made it possible to trust this communication. It ensures that data remains confidential, its integrity is maintained, and the sender's identity is verified. Mastering these concepts is essential for understanding how secure channels like HTTPS and SSH operate in a hostile network environment.

***

## Task 1: Introduction

Cryptography lays the foundation for our digital world. While networking protocols have made it possible for devices spread across the globe to communicate, cryptography has made it possible to trust this communication.

**Learning Objectives:**

* Cryptography key terms and importance.
* Caesar Cipher and historical methods.
* Standard symmetric (AES) and common asymmetric (RSA/ECC) ciphers.
* Basic mathematics (XOR and Modulo) used in cryptography.

Question: I’m ready to start learning about cryptography!

> **Answer:** No answer needed

***

## Task 2: Importance of Cryptography

Cryptography is vital for protecting sensitive data in transit and at rest. One of the most common real-world applications is the protection of financial data.

Question: What is the standard required for handling credit card information?

> **Answer:** PCI DSS

***

## Task 3: Plaintext to Ciphertext

To understand any cryptographic text, we must define the core mechanics of the process:

* **Plaintext:** The original, readable message (document, image, binary data).
* **Ciphertext:** The scrambled, unreadable version after encryption.
* **Cipher:** The algorithm/method developed to convert data back and forth.
* **Key:** A string of bits used by the cipher; must remain secret.
* **Encryption:** The process of converting plaintext to ciphertext.
* **Decryption:** The reverse process, recovering the plaintext.

Below are diagrams of the encryption and decryption process:

<figure><img src="../.gitbook/assets/image (76).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (77).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What do you call the encrypted plaintext?

> **Answer:** ciphertext

Question: What do you call the process that returns the plaintext?

> **Answer:** Decryption

***

## Task 4: Historical Ciphers

Cryptography dates back to 1900 BCE. The **Caesar Cipher** (1st Century BCE) is the simplest, involving a letter shift. Other historical milestones include the **Vigenère cipher** (16th century), the **Enigma machine** (WWII), and the **one-time pad** (Cold War).

Question: Knowing that `XRPCTCRGNEI` was encrypted using Caesar Cipher, what is the original plaintext?

> **Answer:** ICANENCRYPT

***

## Task 5: Types of Encryption

Encryption is divided into two main categories:

#### Symmetric Encryption (Private Key)

Uses the **same key** for both encryption and decryption.

* **DES:** Adopted in 1977; 56-bit key (Now insecure).
* **3DES:** DES applied three times (Deprecated in 2019).
* **AES:** Adopted in 2001; current standard (128, 192, or 256 bits).

<figure><img src="../.gitbook/assets/image (78).png" alt="" width="563"><figcaption></figcaption></figure>

#### Asymmetric Encryption (Public Key)

Uses a **pair of keys** (Public and Private).

* **RSA/Diffie-Hellman:** Recommended minimum key size of 2048 bits.
* **ECC (Elliptic Curve):** Achieves equivalent security with much shorter keys (e.g., 256-bit ECC ≈ 3072-bit RSA).

<figure><img src="../.gitbook/assets/image (79).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Should you trust DES? (Yea/Nay)

> **Answer:** nay

Question: When was AES adopted as an encryption standard?

> **Answer:** 2001

***

## Task 6: Basic Math

Modern cryptography is built on binary arithmetic and remainders.

#### XOR (Exclusive OR)

A bitwise operation: `0 ⊕ 0 = 0`, `0 ⊕ 1 = 1`, `1 ⊕ 0 = 1`, `1 ⊕ 1 = 0`.

* **Properties:** A ⊕ A = 0; A ⊕ 0 = A. This allows XOR to act as a simple symmetric cipher where `(P ⊕ K) ⊕ K = P`.

#### Modulo Operation (%)

The remainder when X is divided by Y.

* **Properties:** Not reversible. `x % 5 = 4` has infinite solutions.
* **Range:** Result is always between `0` and `n-1`.

Question: What’s `1001 ⊕ 1010`?

> **Answer:** 0011

Question: What’s `118613842 % 9091`?

> **Answer:** 3565

Question: What’s `60 % 12`?

> **Answer:** 0

***

## Conclusion

We have covered the importance of cryptography, key terms, historical ciphers, and the types of encryption (Symmetric vs. Asymmetric). We also explored the XOR and Modulo operations that form the mathematical building blocks of modern security.
