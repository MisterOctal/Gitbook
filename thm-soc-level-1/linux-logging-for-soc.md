---
icon: linux
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1757085728640
coverY: 0
coverHeight: 138
---

# Linux Logging for SOC

**Date:** 05.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Exploring authentication, system, and runtime logs on Linux environments, and utilizing tools like `auditd` to monitor and investigate system calls during SOC triage.

As Linux adoption grows across on-premises servers and cloud-native workloads, investigating Linux-targeted intrusions is a critical SOC function. This document outlines the fundamental methodologies for reading, filtering, and interpreting native Linux logs and runtime events.

***

## Task 1: Introduction

The primary objective is to explore the most common Linux logs sent to a SIEM and learn how to view them directly on the host. This includes understanding authentication logs, system logs, runtime monitoring, and the use of the `auditd` tool.

Question: Let's start!

> **Answer:** No answer needed

***

## Task 2: Working With Text Logs

Unlike Windows, Linux records most events into plain text files, meaning they can be read using any standard text editor without specialized software. However, these logs are generally less structured, lacking universal event codes.

Most default Linux logs are located in the `/var/log` directory. A primary example is `/var/log/syslog`, which provides an aggregated stream of various system events.

**Syslog File Content Example**

```bash
root@thm-vm:~$ cat /var/log/syslog | head
[...]
2025-08-13T13:57:49.388941+00:00 thm-vm systemd-timesyncd[268]: Initial clock synchronization to Wed 2025-08-13 13:57:49.387835 UTC.
2025-08-13T13:59:39.970029+00:00 thm-vm systemd[888]: Starting dbus.socket - D-Bus User Message Bus Socket...
2025-08-13T14:02:22.606216+00:00 thm-vm dbus-daemon[564]: [system] Successfully activated service 'org.freedesktop.timedate1'
2025-08-13T14:05:01.999677+00:00 thm-vm CRON[1027]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
[...]
```

**Filtering and Discovering Logs** Because syslog and other files contain thousands of routine events, filtering with command-line utilities like `grep` is essential to isolate relevant data.

```bash
# Or "grep -v CRON" to exclude "CRON" from results
root@thm-vm:~$ cat /var/log/syslog | grep CRON
2025-08-13T14:17:01.025846+00:00 thm-vm CRON[1042]: (root) CMD (cd / && run-parts --report /etc/cron.hourly)
2025-08-13T14:25:01.043238+00:00 thm-vm CRON[1046]: (root) CMD (command -v debian-sa1 > /dev/null && debian-sa1 1 1)
2025-08-13T14:30:01.014532+00:00 thm-vm CRON[1048]: (root) CMD (date > mycrondebug.log)
```

If the exact log file is unknown, searches can be performed across the entire `/var/log` directory recursively:

```bash
# List what's logged by your system (/var/log folder) 
root@thm-vm:~$ ls -l /var/log
drwxr-xr-x  2 root      root               4096 Aug 12 16:41 apt
drwxr-x---  2 root      adm                4096 Aug 12 12:40 audit
-rw-r-----  1 syslog    adm               45399 Aug 13 15:05 auth.log
-rw-r--r--  1 root      root            1361277 Aug 12 16:41 dpkg.log
drwxr-sr-x+ 3 root      systemd-journal    4096 Oct 22  2024 journal
-rw-r-----  1 syslog    adm              214772 Aug 13 13:57 kern.log
-rw-r-----  1 syslog    adm              315798 Aug 13 15:05 syslog
[...]

# Search for potential logins across all logs (/var/log)
root@thm-vm:~$ grep -R -E "auth|login|session" /var/log
[...]
```

_Note: Logging formats, verbosity, and locations can vary significantly across different Linux distributions._

Question: Use the /var/log/syslog file on the VM to answer the questions. Which time server domain did the VM contact to sync its time?

> **Answer:** ntp.ubuntu.com

Question: What is the kernel message from Yama in /var/log/syslog?

> **Answer:** Becoming mindful.

***

## Task 3: Authentication Logs

The `/var/log/auth.log` file (or `/var/log/secure` on RHEL-based systems) is a critical resource for SOC analysts. It records authentication events, user management actions, and commands executed via `sudo`.

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

**Local and Remote Logins** Successful logons and logoffs can be identified by filtering for specific keywords.

```bash
root@thm-vm:~$ cat /var/log/auth.log | grep -E 'session opened|session closed'
# Local, on-keyboard login and logout of Bob (login:session)
2025-08-02T16:04:43 thm-vm login[1138]: pam_unix(login:session): session opened for user bob(uid=1001) by bob(uid=0)
2025-08-02T19:23:08 thm-vm login[1138]: pam_unix(login:session): session closed for user bob
# Remote login examples of Alice (via SSH and then SMB)
2025-08-04T09:09:06 thm-vm sshd[839]: pam_unix(sshd:session): session opened for user alice(uid=1002) by alice(uid=0)
2025-08-04T12:46:13 thm-vm smbd[1795]: pam_unix(samba:session): session opened for user alice(uid=1002) by alice(uid=0)
```

**Cron and Sudo Logins**

```bash
root@thm-vm:~$ cat /var/log/auth.log | grep -E 'session opened|session closed'
# Traces of some cron job launch running as root (cron:session)
2025-08-06T19:35:01 thm-vm CRON[41925]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
2025-08-06T19:35:01 thm-vm CRON[3108]: pam_unix(cron:session): session closed for user root
# Carol running "sudo su" to access root (sudo:session)
2025-08-07T09:12:32 thm-vm sudo: pam_unix(sudo:session): session opened for user root(uid=0) by carol(uid=1003)
```

**SSH-Specific Events** The SSH daemon stores its own structured authentication logs within `auth.log`. These entries typically follow the format: `<is-successful> <auth-method> for <user> from <ip>`.

```bash
root@thm-vm:~$ cat /var/log/auth.log | grep "sshd" | grep -E 'Accepted|Failed'
# Common SSH log format: <is-successful> <auth-method> for <user> from <ip>
2025-08-07T11:21:25 thm-vm sshd[3139]: Failed password for root from 222.124.17.227 port 50293 ssh2
2025-08-07T14:17:40 thm-vm sshd[3139]: Failed password for admin from 138.204.127.54 port 52670 ssh2
2025-08-09T20:30:51 thm-vm sshd[1690]: Accepted publickey for bob from 10.19.92.18 port 55050 ssh2: <key>
```

**Miscellaneous and User Management Events** `auth.log` also tracks account modifications and privilege escalation attempts.

```bash
root@thm-vm:~$ cat /var/log/auth.log | grep -E '(passwd|useradd|usermod|userdel)\['
2023-02-01T11:09:55 thm-vm passwd[644]: password for 'ubuntu' changed by 'root'
2025-08-07T22:11:11 thm-vm userdel[1887]: delete user 'oldbackdoor'
2025-08-07T22:11:29 thm-vm useradd[1878]: new user: name=backdoor, UID=1002, GID=1002, shell=/bin/sh
2025-08-07T22:11:54 thm-vm usermod[1906]: add 'backdoor' to group 'sudo'
2025-08-07T22:11:54 thm-vm usermod[1906]: add 'backdoor' to shadow group 'sudo'
```

**Commands Run With Sudo** Filtering for `COMMAND=` displays specific actions executed with elevated privileges, providing a trail of potentially malicious administrative actions.

```bash
root@thm-vm:~$ cat /var/log/auth.log | grep -E 'COMMAND='
2025-08-07T11:21:49 thm-vm sudo: ubuntu : TTY=pts/0 ; [...] COMMAND=/usr/bin/systemctl stop edr
2025-08-07T11:23:18 thm-vm sudo: ubuntu : TTY=pts/0 ; [...] COMMAND=/usr/bin/ufw status numbered
2025-08-07T11:23:33 thm-vm sudo: ubuntu : TTY=pts/0 ; [...] COMMAND=/usr/bin/su
```

Question: Continue with the VM and use the /var/log/auth.log file. Which IP address failed to log in on multiple users via SSH?

> **Answer:** 10.14.94.82

Question: Which user was created and added to the "sudo" group?

> **Answer:** xerxes

***

## Task 4: Common Linux Logs

Linux tracks diverse events across various files in the `/var/log` directory.

**Generic System Logs:**

* `/var/log/kern.log`: Kernel messages and errors.
* `/var/log/syslog` (or `messages`): Consolidated system events.
* `/var/log/dpkg.log` (or `apt`): Package manager logs for Debian-based systems.
* `/var/log/dnf.log` (or `yum.log`): Package manager logs for RHEL-based systems.

**App-Specific Logs:** Monitoring specific applications requires analyzing their dedicated logs. For example, Nginx web server access logs (`/var/log/nginx/access.log`) record every web request, the client IP, the HTTP method, requested URI, and the HTTP response code.

```bash
root@thm-vm:~$ cat /var/log/nginx/access.log
# Every log line corresponds to a web request to the web server
10.0.1.12 - - [11/08/2025:14:32:10 +0000] "GET / HTTP/1.1" 200 3022
10.0.1.12 - - [11/08/2025:14:32:14 +0000] "GET /login HTTP/1.1" 200 1056
10.0.1.12 - - [11/08/2025:14:33:09 +0000] "POST /login HTTP/1.1" 302 112
10.0.4.99 - - [11/08/2025:17:11:20 +0000] "GET /images/logo.png HTTP/1.1" 200 5432
10.0.5.21 - - [11/08/2025:17:56:23 +0000] "GET /admin HTTP/1.1" 403 104
```

**Bash History:** Bash history records commands executed interactively in the terminal. Commands are temporarily stored in memory during the session and written to the per-user `~/.bash_history` file upon logout.

```bash
ubuntu@thm-vm:~$ cat /home/ubuntu/.bash_history
echo "hello" > world.txt
nano /etc/ssh/sshd_config
sudo su
ubuntu@thm-vm:~$ history
1 echo "hello" > world.txt
2 nano /etc/ssh/sshd_config
3 sudo su
4 ls -la /home/ubuntu
5 cat /home/ubuntu/.bash_history
6 history
```

**Limitations for SOC:** Threat actors can easily evade Bash history logging by utilizing specific bypasses. Due to these bypasses, Bash history is considered an unreliable standalone log source for incident response.

```bash
# Attackers can simply add a leading space to the command to avoid being logged
ubuntu@thm-vm:~$  echo "You will never see me in logs!"

# Attackers can paste their commands in a script to hide them from Bash history
ubuntu@thm-vm:~$ nano legit.sh && ./legit.sh
 
# Attackers can use other shells like /bin/sh that don't save the history like Bash
ubuntu@thm-vm:~$ sh
$ echo "I am no longer tracked by Bash!"
```

Question: According to the VM's package manager logs, which version of unzip was installed on the system?

> **Answer:** 6.0-28ubuntu4.1

Question: What is the flag you see in one of the users' bash history?

> **Answer:** THM{note\_to\_remember}

***

## Task 5: Runtime Monitoring

By default, Linux does not log process creation, file changes, or network-related events. These are collectively known as runtime events.

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

**System Calls:** To monitor runtime events, security tools must interface with the operating system at the kernel level via system calls. Whenever a program opens a file, spawns a process, or initiates a network connection, a specific system call is executed.

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

* There are over 300 system calls in Linux (e.g., `execve` is used to execute a program).
* Modern EDRs and auditing tools function by hooking into and logging these system calls. Since it is nearly impossible for attackers to bypass system calls to interact with the OS, monitoring them is a highly reliable detection mechanism.

Question: Which Linux system call is commonly used to execute a program?

> **Answer:** execve

Question: Can a typical program open a file or create a process bypassing system calls? (Yea/Nay)

> **Answer:** Nay

***

## Task 6: Using Auditd

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Audit Daemon (Auditd):** Auditd is a built-in Linux auditing solution utilized for runtime monitoring. It relies on rules defined in `/etc/audit/rules.d/` to specify which system calls to monitor and which filters to apply. SOC teams balance these rules to focus on high-risk events, as monitoring every system call generates an unmanageable volume of data.

**Analyzing Auditd Logs:** Logs are written to `/var/log/audit/audit.log`. The `ausearch` command is used to format and query these logs readably.

```bash
root@thm-vm:~$ ausearch -i -k proc_wget
----
type=PROCTITLE msg=audit(08/12/25 12:48:19.093:2219) : proctitle=wget [https://files.tryhackme.thm/report.zip](https://files.tryhackme.thm/report.zip)
type=CWD msg=audit(08/12/25 12:48:19.093:2219) : cwd=/root
type=EXECVE msg=audit(08/12/25 12:48:19.093:2219) : argc=2 a0=wget a1=[https://files.tryhackme.thm/report.zip](https://files.tryhackme.thm/report.zip)
type=SYSCALL msg=audit(08/12/25 12:48:19.093:2219) : arch=x86_64 syscall=execve [...] ppid=3752 pid=3888 auid=ubuntu uid=root tty=pts1 exe=/usr/bin/wget key=proc_wget
```

In the example above, auditd splits the event into lines detailing the exact command (`PROCTITLE`), directory (`CWD`), and system call specifics (`pid`, `ppid`, `auid`, `uid`, `exe`, and `key`).

Similarly, monitoring critical file access allows defenders to track unauthorized changes to configurations:

```bash
root@thm-vm:~$ ausearch -i -k file_sshconf
----
type=PROCTITLE msg=audit(08/12/25 13:06:47.656:2240) : proctitle=nano /etc/ssh/sshd_config
type=CWD msg=audit(08/12/25 13:06:47.656:2240) : cwd=/
type=PATH msg=audit(08/12/25 13:06:47.656:2240) : item=0 name=/etc/ssh/sshd_config [...]
type=SYSCALL msg=audit(08/12/25 13:06:47.656:2240) : arch=x86_64 syscall=openat [...] ppid=3752 pid=3899 auid=ubuntu uid=root tty=pts1 exe=/usr/bin/nano key=file_sshconf
```

**Auditd Alternatives:** Because raw auditd output can be cumbersome to parse and ingest into a SIEM, alternatives are frequently deployed:

* **Sysmon for Linux:** Generates structured XML logs similar to its Windows counterpart.
* **Falco:** Highly effective for containerized environments.
* **Osquery:** Allows querying OS state using SQL syntax.
* **EDR Solutions:** Commercial tools that track system calls natively.

Question: When was the secret.thm file opened for the first time? (MM/DD/YY HH:MM:SS)

> **Answer:** 08/13/25 18:36:54

Question: What is the original file name downloaded from GitHub via wget?

> **Answer:** naabu\_2.3.5\_linux\_amd64.zip

Question: Which network range was scanned using the downloaded tool?

> **Answer:** 192.168.50.0/24

***

## Conclusion

Linux logging, while sometimes unstructured and chaotic, contains the necessary artifacts to detect and investigate threats. The `/var/log/` directory houses crucial files like `auth.log` and application-specific logs. For robust detection, particularly regarding process execution and file manipulation, runtime monitoring solutions like `auditd` or Sysmon for Linux are strictly required over unreliable methods like Bash history.

***

## Summary of Commands Learned

<table data-header-hidden><thead><tr><th width="289.20001220703125"></th><th></th></tr></thead><tbody><tr><td>Command / Syntax</td><td>Description / Use Case</td></tr><tr><td><code>cat [file]</code></td><td>Display the complete contents of a file (e.g., <code>cat /var/log/syslog</code>).</td></tr><tr><td><code>cat [file] | head</code></td><td>Display the first 10 lines of a log file to quickly check its structure.</td></tr><tr><td><code>grep [keyword] [file]</code></td><td>Filter the contents of a log file for a specific keyword or pattern.</td></tr><tr><td><code>grep -v [keyword]</code></td><td>Filter <em>out</em> lines containing a specific keyword from the search results.</td></tr><tr><td><code>grep -R -E "[regex]" [folder]</code></td><td>Search recursively across an entire directory (like <code>/var/log</code>) using extended regular expressions.</td></tr><tr><td><code>ls -l /var/log</code></td><td>List the log files, their sizes, owners, and permissions within the <code>/var/log</code> directory.</td></tr><tr><td><code>history</code></td><td>View the interactive bash command history for the current user's session.</td></tr><tr><td><code>ausearch -i -k [key]</code></td><td>Query <code>auditd</code> logs interactively (<code>-i</code> translates to human-readable format) using a specified tracking key (<code>-k</code>).</td></tr></tbody></table>
