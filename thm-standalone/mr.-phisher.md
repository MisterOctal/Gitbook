---
icon: user-ninja
cover: https://assets.tryhackme.com/img/banners/default_tryhackme.png
coverY: 0
coverHeight: 138
---

# Mr. Phisher

**Date:** 26.05.2026&#x20;

**Room Category:** Challenge

**Core Concept:** Malicious document analysis, VBA macros, and basic script conversion.

Microsoft Office documents frequently utilize VBA macros for automation, a feature often weaponized by threat actors to execute system-level commands. This challenge explores methods for safely inspecting these embedded scripts to extract hidden flags and analyze malicious logic without enabling potentially harmful content.

***

## Task 1: Mr. Phisher

The investigation begins by examining the provided `mrphisher.docm` file. Documents with the `.docm` extension are macro-enabled, making them a common vector for malware delivery.

Instead of enabling the macros, the safer approach involves opening the document's macro editor to inspect the raw code. This reveals a hidden VBA macro module containing obfuscated logic.

<figure><img src="../.gitbook/assets/Screenshot 2026-05-26 122428.png" alt=""><figcaption></figcaption></figure>

The extracted VBA code relies on an array of integers and a simple mathematical operation.

```vb
Rem Attribute VBA_ModuleType=VBAModule
Option VBASupport 1
Sub Format()
Dim a()
Dim b As String
a = Array(102, 109, 99, 100, 127, 100, 53, 62, 105, 57, 61, 106, 62, 62, 55, 110, 113, 114, 118, 39, 36, 118, 47, 35, 32, 125, 34, 46, 46, 124, 43, 124, 25, 71, 26, 71, 21, 88)
For i = 0 To UBound(a)
b = b & Chr(a(i) Xor i)
Next
End Sub
```

The script iterates through the array `a`. During each iteration, it performs a bitwise XOR operation (`Xor`) between the current array integer and its index position (`i`). The result is then converted into an ASCII character using the `Chr()` function and appended to a string variable `b`.

To reveal the final string without running the macro in its native environment, the logic can be easily translated into a Python script.

The Python script below replicates the exact behavior of the VBA macro. It defines the list of integers, applies the XOR operation against the index, converts the result to a character, and prints the final output.

```python
# Python conversion of the malicious VBA macro
a = [102, 109, 99, 100, 127, 100, 53, 62, 105, 57, 61, 106, 62, 62, 55, 110, 113, 114, 118, 39, 36, 118, 47, 35, 32, 125, 34, 46, 46, 124, 43, 124, 25, 71, 26, 71, 21, 88]

flag = ""
for i in range(len(a)):
    # XOR the array value with its index and convert to character
    flag += chr(a[i] ^ i)

print(flag)
```

Executing this Python script processes the array and outputs the hidden string, successfully completing the challenge.

Question: Uncover the flag in the email attachment!

> **Answer:** flag{a39a07a239aacd40c948d852a5c9f8d1}
