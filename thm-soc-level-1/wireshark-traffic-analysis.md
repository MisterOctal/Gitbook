---
icon: magnifying-glass
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/6131132af49360005df01ae3/room-content/10f7d168ab59410ddc28a4b1e89fd6d4.png
coverY: 0
coverHeight: 138
---

# Wireshark: Traffic Analysis

**Date:** 21.04.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Applying Wireshark skills to detect network anomalies, identify Nmap scans, uncover ARP poisoning, hunt credentials, and analyze cleartext/encrypted protocols.

This document serves as a research log for identifying real-world network attacks and anomalies through packet analysis. It shifts focus from learning tool operations to applying investigative methodologies to uncover malicious behavior in packet captures (pcaps).

***

## Comprehensive List of Filters Taught

This section catalogs every filter expression taught during this analysis for detecting specific network attacks and anomalies.

#### TCP Flag Filters (General)

* **SYN:** `tcp.flags == 2` or `tcp.flags.syn == 1`
* **ACK:** `tcp.flags == 16` or `tcp.flags.ack == 1`
* **SYN-ACK:** `tcp.flags == 18` or `(tcp.flags.syn == 1) and (tcp.flags.ack == 1)`
* **RST:** `tcp.flags == 4` or `tcp.flags.reset == 1`
* **RST-ACK:** `tcp.flags == 20` or `(tcp.flags.reset == 1) and (tcp.flags.ack == 1)`
* **FIN:** `tcp.flags == 1` or `tcp.flags.fin == 1`

#### Nmap Scan Detection Filters

* **TCP Connect Scan Pattern:** `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024`
* **TCP SYN Scan Pattern:** `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024`
* **UDP Scan Closed Port (ICMP Error):** `icmp.type==3 and icmp.code==3`

#### ARP Poisoning & MITM Filters

* **Global ARP:** `arp`
* **ARP Requests:** `arp.opcode == 1`
* **ARP Responses:** `arp.opcode == 2`
* **ARP Scanning:** `arp.dst.hw_mac==00:00:00:00:00:00`
* **ARP Poisoning Detection:** `arp.duplicate-address-detected or arp.duplicate-address-frame`
* **ARP Flooding:** `((arp) && (arp.opcode == 1)) && (arp.src.hw_mac == target-mac-address)`

#### Host Identification Filters (DHCP, NetBIOS, Kerberos)

* **DHCP Global:** `dhcporbootp`
* **DHCP Request:** `dhcp.option.dhcp == 3` (Look for Option 12: Hostname, 50: Requested IP, 61: MAC)
* **DHCP ACK:** `dhcp.option.dhcp == 5` (Look for Option 15: Domain name)
* **DHCP NAK:** `dhcp.option.dhcp == 6` (Look for Option 56: Message/Rejection details)
* **DHCP Hostname Search:** `dhcp.option.hostname contains "keyword"`
* **NBNS Global:** `nbns`
* **NBNS Name Search:** `nbns.name contains "keyword"`
* **Kerberos Global:** `kerberos`
* **Kerberos Username:** `kerberos.CNameString contains "keyword"` or `kerberos.CNameString and !(kerberos.CNameString contains "$")`
* **Kerberos Protocol/Domain:** `kerberos.pvno == 5`, `kerberos.realm contains ".org"`, `kerberos.SNameString == "krbtg"`

#### Tunneling Filters (ICMP & DNS)

* **ICMP Payload Anomalies:** `data.len > 64 and icmp`
* **DNS Tunneling Patterns:** `dns contains "dnscat"` or `dns.qry.name.len > 15 and !mdns`

#### Cleartext Protocol Filters (FTP)

* **FTP Global:** `ftp`
* **FTP Status Responses:** `ftp.response.code == 211` (System status), `ftp.response.code == 227` (Entering passive mode)
* **FTP Authentication:** `ftp.response.code == 230` (User login successful), `ftp.response.code == 530` (Invalid password)
* **FTP Commands:** `ftp.request.command == "USER"`, `ftp.request.command == "PASS"`, `ftp.request.arg == "password"`
* **FTP Brute Force/Spray Detection:** `(ftp.response.code == 530) and (ftp.response.arg contains "username")` or `(ftp.request.command == "PASS" ) and (ftp.request.arg == "password")`

#### Cleartext Protocol Filters (HTTP)

* **HTTP Global:** `http` or `http2`
* **HTTP Methods:** `http.request.method == "GET"`, `http.request.method == "POST"`
* **HTTP Status Codes:** `http.response.code == 200` (OK), `401` (Unauthorized), `403` (Forbidden), `404` (Not Found), `405` (Method Not Allowed), `503` (Service Unavailable)
* **HTTP Parameters:** `http.user_agent contains "nmap"`, `http.request.uri contains "admin"`, `http.server contains "apache"`, `http.host == "keyword"`, `data-text-lines contains "keyword"`
* **User Agent Hunting:** `(http.user_agent contains "sqlmap") or (http.user_agent contains "Nmap") or (http.user_agent contains "Wfuzz") or (http.user_agent contains "Nikto")`
* **Log4j Vulnerability Hunting:** `(ip contains "jndi") or (ip contains "Exploit")`, `(frame contains "jndi") or (frame contains "Exploit")`, `(http.user_agent contains "$") or (http.user_agent contains "==")`

#### Encrypted Protocol Filters (HTTPS/TLS)

* **TLS Global:** `tls`
* **TLS Client Hello:** `tls.handshake.type == 1`
* **TLS Server Hello:** `tls.handshake.type == 2`
* **SSDP (Simple Service Discovery Protocol):** `ssdp`
* **Isolating Handshakes from SSDP noise:** `(http.request or tls.handshake.type == 1) and !(ssdp)`

***

## Task 1: Introduction

Traffic analysis requires combining network knowledge with filtering techniques to identify anomalies, cleartext data, and malicious activities such as port scanning or man-in-the-middle attacks.

Question: Read the task above.

> **Answer:** No answer needed

***

## Task 2: Nmap Scans

Identifying standard network mapping tools like Nmap involves understanding TCP flags and packet sizes.

* **TCP Connect Scans:** Rely on completing the 3-way handshake. Usually generated by non-privileged users. Expect data, thus window sizes are typically > 1024 bytes.
* **TCP SYN Scans:** "Stealth" scans that do not complete the handshake. Used by privileged users. Window sizes are usually <= 1024 bytes.
* **UDP Scans:** Do not prompt a handshake. Open ports might not reply, while closed UDP ports typically return an ICMP Type 3, Code 3 (Destination Unreachable, Port Unreachable) error.

Question: What is the total number of the "TCP Connect" scans? Filter:

```bash
tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.window_size > 102
```

> **Answer:** 1000

Question: Which scan type is used to scan the TCP port 80? Filter:

```bash
tcp.port == 80
```

> **Answer:** TCP Connect

Question: How many "UDP close port" messages are there? Filter:

```bash
icmp.type == 3 and icmp.code == 3
```

> **Answer:** 1083

Question: Which UDP port in the 55-70 port range is open? Filter:

```bash
udp.dstport >= 55 and udp.port <= 70
```

> **Answer:** 68

***

## Task 3: ARP Poisoning & Man In The Middle!

ARP is a local network protocol without authentication. Adversaries exploit this by sending gratuitous or conflicting ARP packets to manipulate the "IP to MAC address table", placing themselves in the middle of communications.

**Analysis Steps:**

1. Spot duplicate MAC addresses claiming the same IP (usually the gateway).
2. Spot a single MAC claiming multiple IPs (ARP flooding).
3. Investigate higher-layer protocols (like HTTP) to see if traffic is being forwarded to the malicious MAC address.

Question: What is the number of ARP requests crafted by the attacker? Filter:

```bash
arp.opcode == 1 and arp.src.hw_mac == 00:0c:29:e2:18:b4
```

> **Answer:** 284

Question: What is the number of HTTP packets received by the attacker? Filter:

```bash
http and eth.addr == 00:0c:29:e2:18:b4
```

> **Answer:** 90

Question: What is the number of sniffed username\&password entries? Filter:

```bash
urlencoded-form matches ".+"
```

> **Answer:** 6

Question: What is the password of the "Client986"? Filter:

```bash
urlencoded-form matches "client986"
```

> **Answer:** clientnothere!

Question: What is the comment provided by the "Client354"? Filter:

```bash
urlencoded-form matches "client354"
```

> **Answer:** Nice work!

***

## Task 4: Identifying Hosts: DHCP, NetBIOS and Kerberos

Identifying hosts and users is essential for establishing the scope of an infection.

* **DHCP:** Dynamic IP assignment. `DHCP Request` (Option 12) reveals hostnames. `DHCP ACK` reveals domain names.
* **NetBIOS (NBNS):** Legacy protocol for local app communication. Queries reveal hostnames and TTLs.
* **Kerberos:** Microsoft domain authentication. The `CNameString` reveals the username. Strings ending in `$` indicate computer accounts.

Question: What is the MAC address of the host "Galaxy A30"? Filter:

```bash
dhcp.option.hostname contains "A30"
```

> **Answer:** 9a:81:41:cb:96:6c

Question: How many NetBIOS registration requests does the "LIVALJM" workstation have? Filter:

```bash
nbns.name contains "LIVALJM" && nbns.flags.opcode == 5
```

> **Answer:** 16

Question: Which host requested the IP address "172.16.13.85"? Filter:

```bash
dhcp.option.dhcp == 3 && dhcp.option.requested_ip_address == 172.16.13.85
```

> **Answer:** Galaxy-A12

Question: What is the IP address of the user "u5"? (Enter the address in defanged format.) Filter:

```bash
kerberos.CNameString contains "u5"
```

> **Answer:** 10\[.]1\[.]12\[.]2

Question: What is the hostname of the available host in the Kerberos packets? Filter:

```
kerberos.CNameString contains "$"
```

> **Answer:** xp1$

***

## Task 5: Tunneling Traffic: DNS and ICMP

Adversaries tunnel data through trusted protocols to bypass perimeter security.

* **ICMP Tunneling:** High volume of ICMP traffic or packets larger than the standard 64 bytes often indicates data exfiltration or C2 channels.
* **DNS Tunneling:** Unusually long subdomain strings containing encoded commands or data (e.g., `encoded-commands.maliciousdomain.com`).

Question: Use the "Desktop/exercise-pcaps/dns-icmp/icmp-tunnel.pcap" file. Investigate the anomalous packets. Which protocol is used in ICMP tunnelling? Filter:

```bash
data.len > 64 and icmp
```

> **Answer:** SSH

Question: Use the "Desktop/exercise-pcaps/dns-icmp/dns.pcap" file. Investigate the anomalous packets. What is the suspicious main domain address that receives anomalous DNS queries? (Enter the address in defanged format.) Filter:

```bash
dns.qry.name.len > 15 and !mdns
```

> **Answer:** dataexfil\[.]com

***

## Task 6: Cleartext Protocol Analysis: FTP

FTP prioritizes ease of transfer over security. Tracking response codes (`230` for login, `530` for failed login) allows analysts to detect brute-force attacks and credential spraying. Looking at the commands (`USER`, `PASS`, `CWD`, `LIST`) tracks the attacker's progression.

Question: How many incorrect login attempts are there? Filter:

```bash
ftp.response.code == 530
```

> **Answer:** 737

Question: What is the size of the file accessed by the "ftp" account? Filter:

```bash
ftp.response.code == 213
```

> **Answer:** 39424

Question: The adversary uploaded a document to the FTP server. What is the filename? Filter:

```bash
ftp.request.command == "STOR"
```

> **Answer:** resume.doc

Question: The adversary tried to assign special flags to change the executing permissions of the uploaded file. What is the command used by the adversary? Filter:

```bash
ftp contains "CHMOD"
```

> **Answer:** CHMOD 777

***

## Task 7: Cleartext Protocol Analysis: HTTP

HTTP is the backbone of web traffic. Security analysts rely heavily on analyzing HTTP methods, status codes, URIs, and User-Agents.

**User Agent Anomalies:** Modifying the User-Agent is common for attackers. Analyzing this field helps detect audit tools (Nmap, Nikto, sqlmap), subtle misspellings (e.g., "Mozlilla"), or even embedded payloads.

**Log4j Detection:** Log4j attacks often start with HTTP POST requests containing patterns like `jndi:ldap` or `Exploit.class`, typically injected into headers such as the User-Agent.

Question: Investigate the user agents. What is the number of anomalous "user-agent" types? Filter:

```bash
http.user_agent
```

> **Answer:** 6

Question: What is the packet number with a subtle spelling difference in the user agent field? Filter:

```bash
http.user_agent contains "Mozlilla"
```

> **Answer:** 52

Question: Locate the "Log4j" attack starting phase. What is the packet number? Filter:

```bash
(http.user_agent contains "$") or (http.user_agent contains "==")
```

> **Answer:** 444

Question: Locate the "Log4j" attack starting phase and decode the base64 command. What is the IP address contacted by the adversary? (Enter the address in defanged format and exclude "{}".) Filter:

```bash
(http.user_agent contains "$") or (http.user_agent contains "==")
```

> **Answer:** 62\[.]210\[.]130\[.]250

***

## Task 8: Encrypted Protocol Analysis: Decrypting HTTPS

HTTPS uses TLS, encrypting payloads. Analyzing it requires establishing the context of the Client/Server Hello packets, and if available, providing Wireshark with an encryption key log file (`SSLKEYLOGFILE`).

Once the key log file is loaded via `Edit -> Preferences -> Protocols -> TLS`, the encapsulated HTTP data (such as HTTP2 and decompressed headers) becomes visible.

Question: What is the frame number of the "Client Hello" message sent to "accounts.google.com"? Filter:

```bash
(http.request or tls.handshake.type == 1) and !(ssdp)
```

> **Answer:** 16

Question: Decrypt the traffic with the "KeysLogFile.txt" file. What is the number of HTTP2 packets? Filter:

```bash
http2
```

> **Answer:** 115

Question: Go to Frame 322. What is the authority header of the HTTP2 packet? (Enter the address in defanged format.) Filter:

```bash
frame.number == 322
```

> **Answer:** safebrowsing\[.]googleapis\[.]com

Question: Investigate the decrypted packets and find the flag! What is the flag? Filter:

```bash
http2
```

> **Answer:** FLAG{THM-PACKETMASTER}

***

## Task 9: Bonus: Hunt Cleartext Credentials!

Wireshark provides an automated feature (`Tools -> Credentials`) that utilizes protocol dissectors (FTP, HTTP, IMAP, POP, SMTP) to extract cleartext passwords. This accelerates brute-force and credential exposure analysis without needing to manually filter streams.

Question: Use the "Desktop/exercise-pcaps/bonus/Bonus-exercise.pcap" file. What is the packet number of the credentials using "HTTP Basic Auth"? Filter:

```bash
Accessed via UI: Tools -> Credentials
```

> **Answer:** 237

Question: What is the packet number where "empty password" was submitted? Filter:

```bash
Accessed via UI: Tools -> Credentials
```

> **Answer:** 170

***

## Task 10: Bonus: Actionable Results!

Once an anomaly is verified, the network must be secured. Wireshark can rapidly generate firewall rules based on selected packets using `Tools -> Firewall ACL Rules`. This feature supports various formats like Netfilter, Cisco IOS, IPFilter, and IPFirewall.

Question: Use the "Desktop/exercise-pcaps/bonus/Bonus-exercise.pcap" file. Select packet number 99. Create a rule for "IPFirewall (ipfw)". What is the rule for "denying source IPv4 address"? Filter:

```bash
Accessed via UI: Tools -> Firewall ACL Rules (on packet 99)
```

> **Answer:** add deny ip from 10.121.70.151 to any in

Question: Select packet number 231. Create "IPFirewall" rules. What is the rule for "allowing destination MAC address"? Filter:

```bash
Accessed via UI: Tools -> Firewall ACL Rules (on packet 231)
```

> **Answer:** add allow MAC 00:d0:59:aa:af:80 any in

***

## Conclusion

Wireshark provides immense visibility, but analysis must be paired with broader network security knowledge. Moving from packet analysis to automated intrusion detection (Snort, Zeek) is the logical next step in building a robust defense.
