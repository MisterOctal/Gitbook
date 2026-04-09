---
icon: magnifying-glass
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/6645aa8c024f7893371eb7ac/room-content/6645aa8c024f7893371eb7ac-1718961567632
coverY: 0
coverHeight: 138
---

# Digital Forensics Fundamentals

**Date:** 01.04.2026

**Room Category:** Walkthrough

**Core Concept:** Understanding the digital forensics process, preservation of evidence, and practical metadata analysis.

Digital forensics is the application of scientific investigation techniques to digital devices to uncover evidence of cyber crimes. This room explores the methodology used by investigators to ensure evidence is admissible in court.

***

## Task 1: Introduction to Digital Forensics

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Digital forensics is essential in modern law enforcement. When devices like laptops or mobile phones are seized, they are analyzed in labs to find maps, documents, or chat records that can link a suspect to a crime.

Question: Which team was handed the case by law enforcement?

> **Answer:** digital forensics

***

## Task 2: Digital Forensics Methodology

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

The NIST framework defines four key phases for digital forensics:

1. **Collection:** Identifying and securing data sources.
2. **Examination:** Filtering and extracting data of interest.
3. **Analysis:** Correlating evidence to draw conclusions and timelines.
4. **Reporting:** Presenting findings to stakeholders.

Question: Which phase of digital forensics is concerned with correlating the collected data to draw any conclusions from it?

> **Answer:** Analysis

Question: Which phase of digital forensics is concerned with extracting the data of interest from the collected evidence?

> **Answer:** Examination

***

## Task 3: Evidence Acquisition

Integrity is paramount. Investigators must have proper authorization and maintain a strict **Chain of Custody**. To prevent the forensic workstation from altering the evidence during the imaging process, **Write Blockers** are used.

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Which tool is used to ensure data integrity during the collection?

> **Answer:** write blocker

Question: What is the name of the document that has all the details of the collected digital evidence?

> **Answer:** chain of custody

***

## Task 4: Windows Forensics

Windows analysis involves two main types of images:

* **Disk Image:** Non-volatile data (files, history).
* **Memory Image:** Volatile data (RAM, running processes).

Key tools include **FTK Imager** and **Autopsy** for disks, and **DumpIt** and **Volatility** for memory analysis.

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1).png" alt="" width="188"><figcaption></figcaption></figure>

Question: Which type of forensic image is taken to collect the volatile data from the operating system?

> **Answer:** Memory Image

***

## Task 5: Practical Example of Digital Forensics

In this scenario, we investigate the kidnapping of Gado the cat by analyzing a ransom letter and an attached image. Using tools like `pdfinfo` and `exiftool`, we can extract metadata that reveals the author and the location where a photo was taken.

Question: Using pdfinfo, find out the author of the attached PDF file, ransom-letter.pdf.

> **Answer:** Ann Gree Shepherd

Question: Using exiftool or any similar tool, try to find where the kidnappers took the image they attached to their document. What is the name of the street?

> **Answer:** Milk Street

Question: What is the model name of the camera used to take this photo?

> **Answer:** CANON EOS R6

***

## Conclusion

Digital forensics bridges the gap between technology and the legal system. By following the NIST methodology and utilizing specialized tools, investigators can turn hidden digital traces into actionable evidence.
