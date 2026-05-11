---
icon: envelope
cover: >-
  https://images.unsplash.com/photo-1720273238003-079301a7e9b1?q=80&w=1889&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
coverY: 0
coverHeight: 137
---

# CloutHaus 1: Got Clout?

**Date:** 11.05.2026

**Core Concept:** Analyzing the Cyber-Physical link of OSINT and Business Email Compromise (BEC)

This case is a reality check on how "harmless" social media habits translate into corporate risk. Instead of just looking at isolated server logs, I'm tracking how an influencer's public persona becomes the entry point for a full-scale compromise of CloutHaus's internal systems. It is a mix of OSINT and KQL, proving that if you don't secure the human element, your technical defenses don't mean much.

***

## Section 1: Introduction

Afomiya Storm is a trending social media influencer based in D.C. She's known for posting various videos on her personal blog such as "Get Ready With Me", and "Day in My Life" vlogs. However, its important to note that despite her growing audience, her Instagram profile provides a massive peering window into her personal life.

<figure><img src="../.gitbook/assets/image (340).png" alt="" width="375"><figcaption></figcaption></figure>

Question: Based on Afomiya’s Instagram profile, what is the email address she uses for brand deals?

> **Answer:** afomiya.storm@gmail.com

***

## Section 2: Diving Deeper

It seems that her fame is rising, so is her publicly displayed email inbox. But there's a hidden danger in plain sight, she now has to be careful with her emails. She'll have to differentiate which emails are dangerous phishing traps and which are legitmate offers from brands.

Question: Which of the following signs should Afomiya look for to determine if an email offering a brand deal is a phishing attempt?

**A.** The email contains urgent or threatening language, pressuring her to act quickly, such as "Immediate action required" or "Deadline in 24 hours."

**B.** The email asks for sensitive information like her `password`, `credit card details`, or `social security number`.

**C.** The email comes from a domain name that looks suspicious or doesn’t match the legitimate brand’s official domain.

**D.** The email has generic greetings like `"Dear Customer"` instead of addressing her by name or title.

**E.** All of the above.

> **Answer:** E

Afomiya also engages with her followers in Q\&A sessions on her instagram, she answers 'harmless' questions but these questions could potentially be her actual security questions.

<figure><img src="../.gitbook/assets/image (341).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What technique is the threat actor using to manipulate her into revealing personal information that could compromise her email or Instagram account?

> **Answer:** Social Engineering

Using the information from her stories, attackers can attempt to gather intel on a target. This is called OSINT (Open Source Intelligence).

<figure><img src="../.gitbook/assets/image (342).png" alt=""><figcaption></figcaption></figure>

Question: What answer did the attacker enter to try and bypass the security questions? Enter one of the answers the attacker submitted.

> **Answer:** Kidus

Fortunately, despite getting past the first security measure, they can't get past a specific hurdle: a code was sent to Afomiya's phone.

Question: What security measure saved Afomiya's email account from being hacked, despite the threat actor having access to her security question answers?

> **Answer:** MFA

***

## Section 3: Welcome to CloutHaus

Afomiya is now part of a massive brand management company, CloutHaus. She's no longer an independent freelancer, now she works alongside a team and its internal systems. CloutHaus' internal systems log everything such as email and network activity, and uses Azure Data Explorer.

Question: According to CloutHaus internal employee logs, what is Afomiya’s designated professional email?

```kql
Employees | where name contains "afomiya"
```

<figure><img src="../.gitbook/assets/image (343).png" alt=""><figcaption></figcaption></figure>

> **Answer:** afomiya\_storm@clouthaus.com

Question: What is Afomiya’s role with CloutHaus?

```kql
Employees | where name contains "afomiya" | distinct role 
```

<figure><img src="../.gitbook/assets/image (344).png" alt="" width="139"><figcaption></figcaption></figure>

> **Answer:** Influencer Partner

```kql
Employees | where name contains "afomiya" | distinct mfa_enabled
```

<figure><img src="../.gitbook/assets/image (345).png" alt="" width="136"><figcaption></figcaption></figure>

> **Answer:** False

Question: What is the sender’s email address in the email Afomiya received from "Dior"?

```kql
Email
| where recipient == "afomiya_storm@clouthaus.com"
| where subject contains "Dior" or links contains "Dior"
```

<figure><img src="../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>

> **Answer:** collabs@dior-partners.com

Question: What is the subject line of the email Afomiya received from "Dior"?

<figure><img src="../.gitbook/assets/image (348).png" alt="" width="290"><figcaption></figcaption></figure>

> **Answer:** \[EXTERNAL] Exclusive Partnership Opportunity with Dior

Question: What is the link provided in the email?

<figure><img src="../.gitbook/assets/image (349).png" alt="" width="203"><figcaption></figcaption></figure>

> **Answer:** https://super-brand-offer.com/login

Unfortunately, Afomiya feels as if the site is reliable enough and clicks on the link...

Question: When did Afomiya click on the link? Paste the entire timestamp.

```kql
OutboundNetworkEvents
| where url contains "https://super-brand-offer.com/login"
```

<figure><img src="../.gitbook/assets/image (351).png" alt=""><figcaption></figcaption></figure>

> **Answer:** 03/04/2025, 11:20:00

Question: What username did she enter?

> **Answer:** afstorm

Now, its our turn to investigate the IP behind the potentially malicious link.

Question: What is the IP address associated with the domain?

```kql
PassiveDns
| where domain contains "super-brand-offer.com"
```

<figure><img src="../.gitbook/assets/image (352).png" alt="" width="353"><figcaption></figcaption></figure>

> **Answer:** 198.51.100.12

Question: How many distinct domains are linked to the suspicious IP address?

```kql
PassiveDns
| where ip contains "198.51.100.12"
| distinct domain
```

<figure><img src="../.gitbook/assets/image (353).png" alt="" width="154"><figcaption></figcaption></figure>

> **Answer:** 3

Unfortunately, the lack of Afomiya's MFA and her password reuse meant that her account had become ground zero for a cyber incident.

***

## Section 4: Breach

Now that Afomiya's accounts hijacked, the attacker began sending messages to her followers, asking for money and investment opportunities. Unfortunately, some of her fans, being supportive ending up falling for it thinking they were supporting their favorite influencer.

Question: What are the followers really investing in: a great deal or a phishing scam?

> **Answer:** phishing scam

Additionally, the attacker was able to easily locate Afomiya's address due to some of her posts on Instagram, using OSINT. Simply reverse-searching the image she posted reveals the location.

<figure><img src="../.gitbook/assets/image (354).png" alt="" width="472"><figcaption></figcaption></figure>

Question: Based on the images showing the apartment view and amenities from Afomiya’s Instagram post, use a reverse image search to identify the name of the apartment building.

> **Answer:** City Center Apartments

Question: What should you never reuse across different sites to protect your accounts?

> **Answer:** Passwords

***

## Conclusion

This investigation was a solid reminder that Blue Teaming isn't just about staring at a SIEM or writing the perfect KQL query. It’s about understanding the human behind the logs. Afomiya wasn't targeted because of a zero-day exploit or a complex server vulnerability; she was targeted because she was visible.

The transition from her Instagram stories to a corporate breach at CloutHaus highlights exactly why MFA and Identity Protection are non-negotiable in a modern SOC environment. When we look at logs, we aren't just looking at data points we're looking at the digital footprint of a person's mistakes and an attacker's persistence.
