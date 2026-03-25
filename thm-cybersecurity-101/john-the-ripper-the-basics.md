---
icon: knife-kitchen
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1728537810726.png
coverY: 0
coverHeight: 138
---

# John the Ripper: The Basics

**Date:** 25.03.2026

**Room Category:** Walkthrough

**Core Concept:** Learning to use John the Ripper, a high-speed password cracking tool, to crack various hash types, archive passwords, and SSH private keys.

John the Ripper (JtR) is an essential tool for any security professional. Whether you are auditing a server's `/etc/shadow` file for weak passwords or trying to recover an encrypted ZIP archive, John's ability to automatically detect hash types and apply custom rules makes it a powerhouse in the field of offline password cracking.

***

## Task 1: Introduction

John the Ripper is an open-source password security auditing and password recovery tool. It is often referred to as an "adaptable" cracker because it combines several cracking modes into one package and can automatically detect hundreds of hash types. This makes it the first choice for a quick "smoke test" against a list of captured hashes.

Question: Let's begin!

> **Answer:** No answer needed

## Task 2: Basic Terms

To use John effectively, we must understand the tactical difference between a **Dictionary Attack** and a **Brute Force Attack**. A dictionary attack is a targeted approach using a pre-compiled wordlist of likely passwords, whereas a brute force attack attempts every possible mathematical combination of characters. Brute forcing is exhaustive but time-consuming, while dictionary attacks are often faster because they exploit human nature and the tendency to use common words.

Question: What is the most popular extended version of John the Ripper?

> **Answer:** Jumbo John

***

## Task 3: Setting Up Your System

Most Kali Linux installations come with the "Jumbo" version pre-installed, which includes additional community-contributed patches and support for more formats. The effectiveness of John is heavily dependent on the quality of the wordlist used. The most famous one, `rockyou.txt`, originated from a 2009 breach of a social media site and remains a gold standard because it contains over 14 million real-world passwords that humans actually use.

```
# Location of rockyou.txt on Kali
ls /usr/share/wordlists/rockyou.txt.gz
# To unzip it for use:
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```

Question: Which website’s breach was the rockyou.txt wordlist created from?

> **Answer:** rockyou.com

***

## Task 4: Cracking Basic Hashes

John's "Auto-Detect" feature is one of its greatest strengths. If you provide a file containing hashes without specifying a format, John will analyze the length and character set to guess the type. However, for precision and speed, we can manually specify the format using the `--format` flag. This prevents the tool from wasting cycles trying to crack an MD5 hash as if it were a SHA-1.

#### Example Cracking Command:

```
# Cracking a simple MD5 hash using the rockyou wordlist
john --format=md5 --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt
```

| File      | Hash Type | Cracked Value |
| --------- | --------- | ------------- |
| hash1.txt | MD5       | biscuit       |
| hash2.txt | SHA1      | kangeroo      |
| hash3.txt | SHA256    | microphone    |
| hash4.txt | Whirlpool | colossal      |

## Task 5: Cracking Windows Authentication Hashes

Windows stores user passwords as **NTLM** (New Technology LAN Manager) hashes. Unlike modern Linux hashes, NTLM is "unsalted," meaning two users with the same password will have the identical hash. This makes them significantly easier to crack in bulk. In John's syntax, we specify this format as `nt`.

```
# Cracking a Windows NTLM hash
john --format=nt --wordlist=rockyou.txt win_hash.txt
```

Question: What is the cracked value of this password?

> **Answer:** mushroom

## Task 6: Cracking /etc/shadow Hashes

Cracking Linux passwords is a two-step process. User metadata (like usernames and home directories) is stored in `/etc/passwd`, while the actual salted hashes are stored in the restricted `/etc/shadow`. We must use the `unshadow` utility to combine these into a format John can process, allowing the tool to link the cracked password back to a specific user account.

```
# Combining passwd and shadow for John
unshadow passwd.txt shadow.txt > unshadowed.txt

# Cracking the resulting combined file
john --wordlist=rockyou.txt unshadowed.txt
```

Question: What is the root password?

> **Answer:** 1234

## Task 7: Single Crack Mode

Single Crack Mode is often the most efficient way to start an engagement. Instead of using a massive wordlist, John uses the information provided in the username field. It applies "mangling rules" to the username—for example, if the user is `joker`, John will try `Joker`, `joker123`, `jok3r`, etc. This exploits the common habit where users base their passwords on their own names or department titles.

```
# Using Single Crack Mode for high-speed initial attempts
john --single --format=raw-sha256 hash.txt
```

Question: What is Joker’s password?

> **Answer:** Jok3r

## Task 8: Custom Rules

Custom rules are where John truly shines. Modern password policies often require a mix of capital letters, numbers, and symbols. A standard wordlist might have the word "password," but a rule can tell John to automatically try "Password123!" or "p@ssword." These rules allow us to simulate the predictable ways humans try to "complicate" simple words to meet IT requirements.

Question: What do custom rules allow us to exploit?

> **Answer:** Password complexity predictability

Question: What rule would we use to add all capital letters to the end of the word?

> **Answer:** Az”\[A-Z]”

Question: What flag would we use to call a custom rule called THMRules?

> **Answer:** --rule=THMRules

## Task 9 & 10: Cracking Archives (ZIP & RAR)

John cannot crack an archive file directly. We must first use a helper script to extract the "hash" or the "computed signature" of the password from the file metadata. Once we have this hash in a text file, John can work on it like any other password hash. This is a common pattern in John: **Extract, then Crack.**

```
# Step 1: Extract hash from ZIP
zip2john secure.zip > zip_hash.txt
# Step 2: Crack the extracted hash
john --wordlist=rockyou.txt zip_hash.txt

# Step 1: Extract hash from RAR
rar2john secure.rar > rar_hash.txt
# Step 2: Crack the extracted hash
john --wordlist=rockyou.txt rar_hash.txt
```

* **Zip Password:** pass123 | **Flag:** THM{w3ll\_d0n3\_h4sh\_r0y4l}
* **Rar Password:** password | **Flag:** THM{r4r\_4rch1ve5\_th15\_t1m3}

## Task 11: Cracking SSH Keys

SSH private keys (`id_rsa`) are often encrypted with a passphrase. If an attacker steals a private key, they still need this passphrase to use it. Just like archives, we use a conversion script (`ssh2john.py`) to turn the RSA key into a crackable format for John.

```
# Extracting the crackable hash from a protected SSH Private Key
python3 /usr/share/john/ssh2john.py id_rsa > ssh_hash.txt

# Using John to crack the SSH passphrase
john --wordlist=rockyou.txt ssh_hash.txt
```

Question: What is the SSH private key password?

> **Answer:** mango

## Conclusion

John the Ripper is a versatile tool that bridges the gap between different OS authentication methods and file types. By mastering the "2john" conversion tools and understanding how to apply wordlists, I can effectively audit password strength across our entire SOC infrastructure.
