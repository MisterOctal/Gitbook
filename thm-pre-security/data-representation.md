---
icon: circles-overlap
---

# Data Representation

**Date:** 14.03.2026

**Room Category:** Walkthrough

**Core Concept:** How computers represent colors and numbers using binary, hexadecimal, and octal systems.

As I keep grinding through these fundamentals before my HND wraps up in October, understanding how data actually lives inside a computer is super important. If I want to be a top-tier SOC analyst, I need to know how machines actually read data! Ironically, this room talks about... me? The octal number system haha.

***

### Task 1: Introduction

This room kicks things off by explaining how computers view data. While humans find it most convenient to use the decimal system (base-10) because we have ten fingers, computers are limited to two states: 0 and 1. This task sets the foundation for understanding how those two simple digits can represent everything from complex numbers to the colors we see on a screen.

Question: It is time to dive into computer colors.

> **Answer:** No answer needed

***

### Task 2: Representing Colors

Here we look at the basics of color representation by combining Red, Green, and Blue (RGB) light. If we just turn these channels completely on or off, we get 8 different color combinations (2 × 2 × 2 = 8). However, giving each RGB channel 256 levels of intensity gives us over 16 million colors (256 × 256 × 256). Because 256 states require 8 bits (1 byte) of data, a full color takes 3 bytes (24 bits). Reading 24 ones and zeros is a nightmare, so we group them into Hexadecimal characters to make color codes much easier to read (like `#A3EA2A`).

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Preview the color #3BC81E . In one word, what does this color appear to be?

> **Answer:** green

Question: What is the binary representation of the color #EB0037 ?

> **Answer:** 11101011 00000000 00110111

Question: What is the decimal representation of the color #D4D8DF ?

> **Answer:** 212 216 223

***

### Task 3: Numbers: From Decimal to Hexadecimal

This task is a deep dive into the math behind binary (base-2) numbers. On an electronic level, everything boils down to a transistor either passing or blocking current (high/low voltage). Converting binary to decimal just involves multiplying by powers of 2. Hexadecimal (base-16) is heavily used because one hex digit (0-9, A-F) represents exactly 4 bits (a nibble), making two hex digits a perfect representation of a full 8-bit byte.

Question: What is the hexadecimal FF in binary?

> **Answer:** 1111 1111

Question: What is the hexadecimal AB in decimal?

> **Answer:** 171

Question: Convert the hexadecimal FF FF FF to decimal. After you round up the decimal value to the nearest million, how many millions is that?

> **Answer:** 17

***

### Conclusion

That wraps up "Data Representation"! It was a fantastic refresher covering decimal, binary, hexadecimal, and even octal systems. Knowing how bits and bytes form everything from IP addresses to hex color codes is foundational. Whether I'm reading packet captures in Wireshark or analyzing malware memory dumps, knowing how to quickly translate hex into decimal or binary is a vital skill for a SOC analyst.
