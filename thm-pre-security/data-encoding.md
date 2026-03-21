---
icon: sigma
---

# Data Encoding

**Date:** 14.03.2026

**Room Category:** Walkthrough

**Core Concept:** How computers encode characters, from ASCII to Unicode's UTF.

Following up on the Data Representation room, it is time to look at how numbers are actually translated into the text, punctuation, and emojis we use every day. If everything is just 1s and 0s to a computer, there has to be a universal agreement on what those numbers mean. Understanding character encoding is crucial because it explains why things sometimes look like gibberish and how data is fundamentally structured and stored.

***

### Task 1: Introduction

This room builds on the concept that data lives as bits and numbers in memory. Encoding is simply the agreed-upon mapping between those numbers and their meanings (like assigning a specific number to the letter "A"). When you open a file and see weird, unreadable characters, it is usually because the file was saved with one encoding standard and opened with a different one.

Question: It is time to dive into encoding.

> **Answer:** No answer needed

***

### Task 2: ASCII

ASCII (American Standard Code for Information Interchange) is one of the earliest encoding standards. It uses 7 bits (numbers 0-127) to represent English letters, digits, and basic punctuation. For example, the computer reads the hexadecimal number `41` and displays a capital `A`. However, because it only covers 128 characters, it completely fails when trying to represent letters from other languages, leading to the creation of various extended standards that often conflicted with each other.

Question: What is the ASCII code in decimal for the character @ ?

> **Answer:** 64

Question: What is the character that has the ASCII code of 35 in decimal?

> **Answer:** #

Question: What is the name of the character that has the ASCII code of 7?

> **Answer:** BEL

***

### Task 3: Unicode

To solve the chaos of conflicting encoding standards, Unicode was created. It is a universal character encoding standard that assigns a unique number to every character across all languages, including complex scripts like Chinese and Japanese, and even emojis! UTF-8 is the most common implementation on the web because it dynamically uses 1 to 4 bytes depending on the character, saving space while remaining backwards-compatible with ASCII.

Question: What is the UTF-32 encoding of 😌?

> **Answer:** U+0001F60C

Question: What is the UTF-16 encoding of シ? Note that ツ and シ are two different characters.

> **Answer:** U+30B7

Question: What is the character that has the following UTF-32 encoding U+0001F60E ?

> **Answer:** 😎

Question: What is the character that has the following UTF-16 encoding U+2658 ?

> **Answer:** ♘

***

### Conclusion

That wraps up the Data Encoding room! We covered the limitations of the original ASCII standard and saw how Unicode (specifically UTF-8, UTF-16, and UTF-32) stepped in to unify text representation across the globe. Understanding how characters and symbols are encoded is just another layer of understanding how systems process and store information.
