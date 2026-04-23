---
icon: folder-open
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/5e8dd9a4a45e18443162feab-1759389277781
coverY: 0
coverHeight: 138
---

# Data Exfiltration Detection

**Date:** 23.04.2026

**Room Category:** Walkthrough

**Core Concept:** Detecting unauthorized data transfers across various protocols including DNS, FTP, HTTP, and ICMP using SIEM (Splunk) and packet analysis (Wireshark).

Data exfiltration is the unauthorized transfer of sensitive information from a compromised network to an external destination. For a SOC analyst, detecting exfiltration is a race against time to stop data loss. This room covers the common techniques used by adversaries, such as tunneling and protocol abuse and the specific indicators of attack (IoAs) used to catch them.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (290).png" alt="" width="186"><figcaption></figcaption></figure>

Exfiltration is a primary objective for attackers who have successfully breached a network. This room focuses on identifying the footprints left behind during the transfer phase.

Question: Continue to the next task.

> **Answer:** No answer needed

***

## Task 2: Lab Connection

The practical environment includes a desktop with PCAP files and a pre-configured Splunk instance.

Question: Connect with the lab.

> **Answer:** No answer needed

***

## Task 3: Data Exfil: Overview, techniques, and Indicators

Adversaries exfiltrate data for financial gain, espionage, or extortion. Detection requires monitoring both network-based indicators (large POSTs, DNS tunneling) and host-based indicators (unusual process activity like `rclone` or `awscli`).

Question: Exfiltrating the data through HTTP comes under which technique?

> **Answer:** Network-based

***

## Task 4: Detection: Data Exfil through DNS Tunneling

DNS tunneling hides data inside DNS queries, often bypassing firewalls that allow port 53 traffic. Indicators include high volumes of requests to a single domain, unusually long subdomain strings, and high-entropy query names.

**Wireshark Filters:**

```bash
# Filter for all DNS traffic
dns

# Filter for DNS queries with no response
dns.flags.response == 0

# Find long queries with frame lengths over 70
dns && frame.len > 70
```

**Splunk Queries:**

```bash
# Display statistics of DNS queries per source IP
index="data_exfil" sourcetype="DNS_logs" | stats count by src_ip

# Identify long query names (length over 30)
index="data_exfil" sourcetype="DNS_logs" | where len(query) > 30
```

Question: What is the suspicious domain receiving the DNS traffic?

> **Answer:** tunnelcorp.net

Question: How many suspicious traffic/logs related to dns tunneling were observed?

> **Answer:** 315

Question: Which local IP sent the maximum number of suspicious requests?

> **Answer:** 192.168.1.103

***

## Task 5: Detection: Data Exfil through FTP

FTP is often used for bulk data transfer. Analysts should look for cleartext credentials (`USER`/`PASS`) and the `STOR` command, which indicates a file upload to a server.

**Wireshark Filters:**

```bash
# Isolate FTP control and data sessions
ftp || ftp-data

# Filter for login attempts
ftp.request.command == "USER" || ftp.request.command == "PASS"

# Filter for file upload commands
ftp contains "STOR"

# Filter for large payloads (adjust length as needed)
ftp && frame.len > 90
```

Question: How many connections were observed from the guest account?

> **Answer:** 5

Question: Apply the filter; what is the name of the customer-related file exfiltrated from the root account?

> **Answer:** customer\_data.xlsx

Question: Which internal IP was found to be sending the largest payload to an external IP?

> **Answer:** 192.168.1.105

Question: What is the flag hidden inside the ftp stream transferring the CSV file to the suspicious IP?

> **Answer:** THM{ftp\_exfil\_hidden\_flag}

***

## Task 6: Detection: Data Exfil via HTTP

HTTP is the most common channel for exfiltration because it blends with normal web traffic. SOC analysts look for large `POST` requests, encoded data in `GET` query strings, or the use of legitimate cloud storage APIs for staging.

**Splunk Queries:**

```bash
# Filter for POST methods
index="data_exfil" sourcetype="http_logs" method=POST

# Analyze average bytes sent to identify large uploads
index="data_exfil" sourcetype="http_logs" method=POST 
| stats count avg(bytes_sent) max(bytes_sent) min(bytes_sent) by domain 
| sort - count

# Isolate large payloads (over 600 bytes)
index="data_exfil" sourcetype="http_logs" method=POST bytes_sent > 600 
| table _time src_ip uri domain dst_ip bytes_sent 
| sort - bytes_sent
```

**Wireshark Filters:**

```bash
# Filter for POST requests with specific frame lengths
http.request.method == "POST" and frame.len > 750
```

Question: Which internal compromised host was used to exfiltrate this sensitive data?

> **Answer:** 192.168.1.103

Question: What's the flag hidden inside the exfiltrated data?

> **Answer:** THM{http\_raw\_3xf1ltr4t10n\_succ3ss}

***

## Task 7: Detection: Data Exfiltration via ICMP

ICMP tunneling involves embedding data into the payload of echo requests or replies. Since most "pings" have a standard small payload, any ICMP packet with a large frame length is highly suspicious.

**Wireshark Filters:**

```bash
# Isolate ICMP Echo Request packets
icmp.type == 8

# Filter for Echo Requests with suspicious frame lengths (standard is ~74 bytes)
icmp.type == 8 and frame.len > 100
```

Question: What is the flag found in the exfiltrated data through ICMP?

> **Answer:** THM{1cmp\_3ch0\_3xf1ltr4t10n\_succ3ss}

***

## Conclusion

Data exfiltration is a high-impact threat that relies on stealth and protocol abuse. Effective detection requires a layered approach: monitoring DNS for abnormal volume, auditing FTP for unauthorized file uploads, and inspecting HTTP and ICMP for unusual payload sizes. By correlating network flows with endpoint logs, SOC analysts can identify staged data before it leaves the organization.
