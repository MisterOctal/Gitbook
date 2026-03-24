---
icon: hashtag-lock
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1728537810726.png
coverY: 0
coverHeight: 138
---

# Hashing Basics

**Date:** 24.03.2026

**Room Category:** Walkthrough / Cyber Security 101

**Core Concept:** Understanding one-way cryptographic functions, data integrity verification, and secure password storage mechanisms.

Cryptography lays the foundation for our digital world. While networking protocols have made it possible for devices spread across the globe to communicate, cryptography has made it possible to trust this communication. It ensures that data remains confidential, its integrity is maintained, and the sender's identity is verified. In this room, we focus on hashing a unique "fingerprint" for data that is essential for verifying that files haven't been tampered with and for protecting user credentials.

***

## Task 1: Introduction

A **hash value** is a fixed-size string of characters computed by a hash function. It takes an input of any size and returns an output of fixed length. Key uses include comparing large files for identity without bit-by-bit comparison and securing passwords.

Question: Let's begin!

> **Answer:** No answer needed

***

## Task 2: Hash Functions

Hash functions are one-way algorithms. Even a single bit change in the input (e.g., changing 'T' to 'U') results in a completely different hash. Common algorithms include MD5, SHA-1, and SHA-256.

**Common Commands:**

* `md5sum <file>`: Generates a 128-bit (16 byte) MD5 hash.
* `sha256sum <file>`: Generates a 256-bit SHA-256 hash.

Question: What is the SHA256 hash of the `passport.jpg` file in `~/Hashing-Basics/Task-2`?

> **Answer:** 77148c6f605a8df855f2b764bcc3be749d7db814f5f79134d2aa539a64b61f02

Question: What is the output size in bytes of the MD5 hash function?

> **Answer:** 16

Question: If you have an 8-bit hash output, how many possible hash values are there?

> **Answer:** 256

***

## Task 3: Insecure Password Storage for Authentication

Storing passwords in plaintext or using weak/deprecated encryption is a major security risk. If a database is leaked, attackers can immediately see user credentials.

Question: What is the 20th password in rockyou.txt?

> **Answer:** qwerty

***

## Task 4: Using Hashing for Secure Password Storage

To defend against **Rainbow Tables** (precomputed lists of hashes), we use **Salting**. A salt is a random string added to the password before hashing, ensuring that even identical passwords result in different hashes.

* **Rainbow Table:** A lookup table that trades disk space for time to reverse hashes.
* **Salt:** Random data unique to each user; does not need to be secret but prevents bulk cracking.

Websites like CrackStation(opens in new tab) and Hashes.com(opens in new tab) internally use massive rainbow tables to provide fast password cracking for hashes without salts. Doing a lookup in a sorted list of hashes is quicker than trying to crack the hash.

<figure><img src="../.gitbook/assets/image (81).png" alt="" width="563"><figcaption></figcaption></figure>

Question: Manually check the hash "4c5923b6a6fac7b7355f53bfe2b8f8c1" using the rainbow table.

> **Answer:** inS3CyourP4\$$

Question: Crack the hash "5b31f93c09ad1d065c0491b764d04933" using an online tool.

> **Answer:** tryhackme

Question: Should you encrypt passwords in password-verification systems? (Yea/Nay)

> **Answer:** Nay

***

## Task 5: Recognizing Password Hashes

In offensive security, recognizing the hash type is the first step to cracking it.

* **Linux:** Hashes are stored in `/etc/shadow` in the format `$prefix$options$salt$hash`.
* **Windows:** Uses **NTLM** (a variant of MD4).

**Common Prefixes:**

* `$1$`: MD5
* `$5$`: SHA-256
* `$6$`: SHA-512
* `$y$`: Yescrypt (Modern Linux)
* `$2a$`: Bcrypt

Question: What is the hash size in yescrypt?

> **Answer:** 256

Question: What’s the Hash-Mode listed for Cisco-ASA MD5?

> **Answer:** 2410

Question: What hashing algorithm is used in Cisco-IOS if it starts with $9$?

> **Answer:** scrypt

***

## Task 6: Password Cracking

We use tools like **Hashcat** or **John the Ripper** to recover plaintexts.

* `hashcat -m <type> -a 0 <hashfile> <wordlist>`

Question: Use hashcat to crack the hash `$2a$06$7yoU3Ng8dHTXphAg913cyO6Bjs3K5lBnwq5FJyA6d01pMSrddr1ZG`.

> **Answer:** 85208520

Question: Use hashcat to crack the SHA2-256 hash `9eb7ee7f551d2f0ac684981bd1f1e2fa4a37590199636753efe614d4db30e8e1`.

> **Answer:** halloween

Question: Use hashcat to crack the hash `$6$GQXVvW4EuM$ehD6jWiMsfNorxy5SINsgdlxmAEl3.yif0/c3NqzGLa0P.S7KRDYjycw5bnYkF5ZtB8wQy8KnskuWQS3Yr1wQ0`.

> **Answer:** spaceman

Question: Crack the hash `b6b0d451bbf6fed658659a9e7e5598fe`.

> **Answer:** funforyou

***

## Task 7: Hashing for Integrity Checking

Hashing ensures files haven't been modified. Developers provide checksums (like `.sha256` files) so you can verify downloads.

**HMAC (Keyed-Hash Message Authentication Code):** Uses a secret key + a hash function to verify both **integrity** and **authenticity**. Formula: `HMAC(K,M) = H((K ^ opad) || H((K ^ ipad) || M))`

<figure><img src="../.gitbook/assets/image (82).png" alt="" width="330"><figcaption></figcaption></figure>

Question: What is SHA256 hash of `libgcrypt-1.11.0.tar.bz2` found in `~/Hashing-Basics/Task-7`?

> **Answer:** 09120c9867ce7f2081d6aaa1775386b98c2f2f246135761aae47d81f58685b9c

Question: What’s the hashcat mode number for HMAC-SHA512 (key = $pass)?

> **Answer:** 1750

***

## Conclusion

It is critical to distinguish between these three:

1. **Hashing:** One-way, fixed size, used for integrity/passwords.
2. **Encoding:** Reversible, changes format for compatibility (e.g., Base64).
3. **Encryption:** Reversible with a key, protects confidentiality.

Question: Use base64 to decode `RU5jb2RlREVjb2RlCg==`. What is the original word?

> **Answer:** ENcodeDEcode

Question: Ensure you have noted the various concepts and tools.

> **Answer:** No answer needed

In conclusion, hashing is the "fingerprint" of the digital world. We've explored how it protects passwords through salting, ensures file integrity via checksums, and how tools like Hashcat can be used to recover plaintexts from hashes when proper security measures aren't in place.
