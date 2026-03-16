---
icon: key
---

# Cryptography Concepts

Date: 16.03.2026

Room Category: Walkthrough

Core Concept: An introduction to cryptography, covering symmetric and asymmetric encryption, and how they protect data in transit.

Crytpography is a concept that I feel fairly intimidated by. Understanding the basics of how data is encrypted, decrypted, and securely shared forms the backbone of internet communication and security.

***

### Task 1: Introduction

When you visit a website and see the little padlock icon in your browser, it means your connection is secure. But what is actually stopping someone from reading or modifying your data as it travels across the internet? This room introduces the concept of cryptography, which acts as the ultimate safeguard for maintaining confidentiality and integrity when data is in transit.

Question: Let's get started.

> Answer: No answer needed

***

### Task 2: Hiding Information - Symmetric Encryption

To understand encryption, you have to know the terminology. Plaintext is a readable message. Ciphertext is the scrambled, unreadable version. The algorithm is the public mathematical process used to scramble the data, and the key is the secret ingredient that locks or unlocks it. In symmetric encryption, like the classic Caesar cipher, the exact same key is used to both encrypt and decrypt the message. It is incredibly fast and efficient for bulk data.

<figure><img src="../.gitbook/assets/image (47).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (48).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What's the flag you received after completing all levels of the Secret Message Rescue game?

> Answer: THM{CAESAR\_CIPHER\_MASTER\_2026}

Question: Using the Caesar cipher with a key of 5, what does `CYBER` become when encoded? (Uppercase, no spaces.)<br>

> Answer: hdgjw

Question: Using the Caesar cipher, find the correct key and decode the following secret message: `FVZCYR PNRFNE PVCURE`.

> Answer: the system is at risk

***

### Task 3: Sharing Keys Safely: Asymmetric Encryption

Symmetric encryption is fast, but it has a massive flaw known as the key distribution problem. If Alice and Bob have never met, how can they securely share the symmetric key over the internet without someone intercepting it? Asymmetric encryption solves this by using two mathematically linked keys: a public key that anyone can see, and a private key that is kept completely secret. Modern systems like HTTPS use a hybrid approach: they use asymmetric encryption to securely share a key, and then switch to faster symmetric encryption for the actual data transfer.

<figure><img src="../.gitbook/assets/image (49).png" alt="" width="563"><figcaption></figcaption></figure>

Question: With asymmetric encryption, which key is kept secret?

> Answer: Private Key

Question: With asymmetric encryption, Alice can encrypt a message using Bob's public key, and only Bob's private key can decrypt it. Yay or Nay?

> Answer: Yay

Question: What problem does asymmetric solve that symmetric cannot?

> Answer: Key Kistribution

Question: After the initial asymmetric exchange in HTTPS, what encryption type handles the bulk data?

> Answer: Symmetric

***

### Conclusion

This room provided a solid foundation on how cryptography secures our everyday digital encounters. We covered the differences between symmetric and asymmetric encryption, the problem of key distribution, and how real world systems combine both methods to achieve secure and fast communication. Protecting confidentiality and integrity relies heavily on these cryptographic concepts!
