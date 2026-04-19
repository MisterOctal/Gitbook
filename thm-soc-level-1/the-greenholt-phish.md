---
icon: fishing-rod
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/8c5ab5c62547be1c06c33d5e9c96e129.png
coverY: 0
coverHeight: 138
---

# The Greenholt Phish

**Date:** 17.04.2026&#x20;

**Room Category:** Challenge&#x20;

**Core Concept:** Analyzing a suspicious email (EML) file, extracting headers/artifacts, and performing infrastructure and malware reconnaissance.

A sales executive at Greenholt PLC reported a suspicious email from a customer. The message contains several red flags: a generic greeting, a request for a financial transfer, and an unsolicited attachment. As a SOC analyst, your objective is to perform a deep-dive analysis of the email to determine its origin and potential maliciousness.

***

## Analysis Methodology

To solve this challenge, the following tools and investigative steps were utilized:

* **Email Client / Text Editor:** Used to open the `challenge.eml` file to view the body and extract the Subject line and Display Name.
* **Header Analysis (in Thunderbird):** Examining the raw message source to find the `Reply-To` field and the `X-Originating-IP`.
* **WHOIS (whatismyip.com):** Used the originating IP address to perform a WHOIS lookup to identify the hosting provider (Hostwinds LLC).
* **SPF/DMARC Lookups (EasyDMARC):** Entered the domain found in the `Return-Path` header into EasyDMARC's lookup tools to retrieve the raw DNS TXT records.
* **Hash Analysis (sha256sum):** Calculated the file's unique fingerprint to search for existing global detections.
* **VirusTotal:** Uploaded the hash to check the file size, actual file type, and community reputation.

***

## Phase 1: Email Artifact Extraction

The first step in any email investigation is to gather the "low-hanging fruit" from the user interface and the message headers.

Question: What is the Transfer Reference Number listed in the email's Subject line?

> **Answer:** 09674321

Question: What is the display name of the sender?

> **Answer:** Mr. James Jackson

Question: What is the sender's email address?

> **Answer:** info@mutawamarine.com

Question: What email address will receive a reply to this email?

> **Answer:** info.mutawamarine@mail.com

***

## Phase 2: Infrastructure Reconnaissance

By looking at the raw message source, we can identify the server that actually handed off the email and check the domain's security posture. Below is the email header:

<pre class="language-yaml"><code class="lang-yaml">X-Atlas-Received: from 10.201.192.162 by atlas125.free.mail.bf1.yahoo.com with http; Wed, 10 Jun 2020 05:58:55 +0000
<strong>Return-Path: &#x3C;info@mutawamarine.com>
</strong>Received: from x.x.x.x (EHLO sub.redacted.com)
 by atlas125.free.mail.bf1.yahoo.com with SMTPs; Wed, 10 Jun 2020 05:58:55 +0000
X-Originating-Ip: [x.x.x.x]
Received-SPF: fail (domain of mutawamarine.com does not designate x.x.x.x as permitted sender)
Authentication-Results: atlas125.free.mail.bf1.yahoo.com;
 spf=fail smtp.mailfrom=mutawamarine.com;
 dmarc=unknown
X-Apparently-To: redacted@yahoo.com; Wed, 10 Jun 2020 05:58:55 +0000
X-YMailISG: CA2XOWoWLDuMav_xVT1F_okXM35Y6SWpmP6zsE6LeQRxoxw4
 YjzuEZUWxEEJzHhUGbKbpzCq7GFztoIFDbqKMkWunxnYA6aofbh6xusqm_FJ
 x591PPWDY5NhvW7H.Pwb9o9VmzNhbgKs3KzMN9IO7Uh5jf5y6rUw.dSshjuv
 j1RgxZYshquA.RCedSbTlM1pyxBT4LoSfMkWr0E4FgJSW3l9zg8wK35sizWP
 GqFHAyID0v.GOU7dBURvMp8asqQiPa4kYC7v0oQTvmUEDtENPAjmCnfcajpo
 gei5zs471gDrr3JWxiUMUTyChHRw9nCczLepgGA2Jt_MdbCZ7qgFqWMvvo1I
 nIXkl35mwKec90ZCIPJc6tCHAQyFkE.030_.0VmK_brmLt5oqQiGBYmyCV2i
 CwhdwdTwYkUIdgler50ESBs5mHXSqnNvtmpQoRjMPdqSXiB7yvSIFaiF5rQ8
 OTEw0w1CkWz4gxhNU4FH4Iub03b9TLvUoX9KLEFx3Del5yPTF8xXY7NY_kzA
 aCwKTjp4FaeT2Mk1Pq5P48DF.dB6hdMTmCoowuSwouW2M9Yp4euqKNzGrlcf
 2KcRMROfFVcKDwXSaHw4tMhKvXSH0KiWMVFpXPaMmt2c0cklVpwZMyql8w8W
 PlponB1e66yiIqNuYV.vt64i52HFh0jNwcuuohMo7MA7DmMP.OtkdwLlUqLS
 68AfikwfW3Sppf_pTqtP6NPf2wuAsJIaT7_QQ.4x3khgYrC4jTmXjVBWDVRV
 wT0AdlQ716U8TVp.0AKvevKfMzfZoTOTsGLuQU.w8uZhv_6mwKB4sW7Pbhbr
 B1RJadC0va2CJiCbAC1Qnapm5egSIExqkhboy8iOUfzOqkD1a1_tn5nv1IxD
 bCqerO7cnAjpN1amfUvC8gjD345qb6k9l7h7a8TFsv_67Nkrok_.M4_MRZcf
 .iuxPcffE2r1ocaSfWQg6yof0WQta51sbWQidg7B_4XR2_6cbg8Ui39t2v2Y
 bgWISohtB1urfpr.b1SANr7fvE2Zzvzjz4_4PbBtBDevFUB7Pjq0GiAe_Nx_
 YpW8pLoFasyi1k4T9f5e5ryqAu.HToIegVimVa4xwuzjbNvaE7Tsm4m3vepb
 zGZI1BWuDLQ-
X-Originating-IP: [x.x.x.x]
Received: from 10.197.41.148  (EHLO sub.redacted.com) (x.x.x.x)
  by mta4212.mail.bf1.yahoo.com with SMTP; Wed, 10 Jun 2020 05:58:54 +0000
<strong>Received: from hwsrv-737338.hostwindsdns.com ([192.119.71.157]:51810 helo=mutawamarine.com)
</strong>	by sub.redacted.com with esmtp (Exim 4.80)
	(envelope-from &#x3C;info@mutawamarine.com>)
	id 1jissD-0004g5-Ts
	for webmaster@redacted.org; Wed, 10 Jun 2020 01:02:04 -0400
<strong>Reply-To: "Mr. James Jackson" &#x3C;info.mutawamarine@mail.com>
</strong><strong>From: "Mr. James Jackson" &#x3C;info@mutawamarine.com>
</strong>To: webmaster@redacted.org
Subject: webmaster@redacted.org your: Transfer Reference Number:(09674321)
Date: 09 Jun 2020 22:58:27 -0700
Message-ID: &#x3C;20200609225823.DFAEAAF31A6B7414@mutawamarine.com>
MIME-Version: 1.0
Content-Type: multipart/mixed;
	boundary="----=_NextPart_000_0012_BDB07B06.81B59493"
X-Spam-Status: No, score=-0.5
X-Spam-Score: -4
X-Spam-Bar: /
X-Ham-Report: Spam detection software, running on the system "sub.redacted.com", has
 identified this incoming email as possible spam.  The original message
 has been attached to this so you can view it (if it isn't spam) or label
 similar future email.  If you have any questions, see
 the administrator of that system for details.
 
 Content preview:  Good day webmaster@redacted.org , As instructed,
    funds has been transferred to your account this morning via SWIFT. Details
    are as below and a receipt of payment is attached. [...] 
 
 Content analysis details:   (-0.5 points, 5.0 required)
 
  pts rule name              description
 ---- ---------------------- --------------------------------------------------
 -0.0 RCVD_IN_DNSWL_NONE     RBL: Sender listed at https://www.dnswl.org/, no
                             trust
                             [192.119.71.157 listed in list.dnswl.org]
 -0.0 BAYES_40               BODY: Bayes spam probability is 20 to 40%
                             [score: 0.3644]
  0.0 HTML_MESSAGE           BODY: HTML included in message
  0.1 MIME_HTML_ONLY         BODY: Message only has text/html MIME parts
  0.4 HTML_MIME_NO_HTML_TAG  HTML-only message, but there is no HTML tag
 -0.9 AWL                    AWL: Adjusted score from AWL reputation of From: address
X-Spam-Flag: NO
</code></pre>

Question: What is the originating IP address of this email?

> **Answer:** 192.119.71.157

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Question: Who is the owner of the originating IP?

> **Answer:** Hostwinds LLC

<table data-header-hidden><thead><tr><th width="96.20001220703125"></th><th></th></tr></thead><tbody><tr><td>Check</td><td>Value</td></tr><tr><td><strong>SPF</strong></td><td>v=spf1 include:spf.protection.outlook.com -all</td></tr><tr><td><strong>DMARC</strong></td><td>v=DMARC1; p=quarantine; fo=1</td></tr></tbody></table>

Question: Run an SPF record check on the Return-Path domain identified in the email headers. What is the full SPF record for this domain?

> **Answer:** v=spf1 include:spf.protection.outlook.com -all

Question: Perform a DMARC lookup for the Return-Path domain found in the email headers. What is the complete DMARC record for this domain?

> **Answer:** v=DMARC1; p=quarantine; fo=1

***

## Phase 3: Malware & Attachment Analysis

The final phase involves analyzing the payload. Phishers often use double extensions or compressed formats to bypass basic filters.

Question: What is the file name of the attachment found in the email?

> **Answer:** SWT\_#09674321\_\_\_\_PDF\_\_.CAB

Question: Using the sha256sum command, what is the SHA256 hash of the file?

> **Answer:** 2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Question: Investigate the file hash from the previous question using VirusTotal. What is the attachment's file size in KB?

> **Answer:** 400.26 KB

Question: What is the actual file type of the attachment?

> **Answer:** RAR

***

## Conclusion

The investigation confirms that the email is a phishing attempt. While the domain uses legitimate protection (SPF/DMARC), the `Reply-To` address differs from the sender, and the originating IP belongs to a VPS provider rather than a standard corporate mail server. The attachment, while appearing to be a PDF, is actually a Cabinet (CAB) archive, a common tactic for delivering malware.
