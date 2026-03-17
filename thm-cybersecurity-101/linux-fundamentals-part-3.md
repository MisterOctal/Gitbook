---
icon: linux
---

# Linux Fundamentals Part 3

Date: 17.03.2026

Room Category: Walkthrough

Core Concept: Powering up Linux skills by learning terminal text editors, general utilities, process management, automation, package management, and system logging.

This final installment of the Linux Fundamentals series moves beyond basic navigation and focuses on the day-to-day utilities used by system administrators and security professionals. Knowing how to edit files directly in the terminal, manage running processes, schedule automated tasks, and review system logs are mandatory skills for interacting with and securing any Linux environment.

***

## Task 1: Introduction

This room concludes the fundamentals module by showcasing practical applications and utilities. It advances your skills by introducing text editors, how to download and transfer files over the command line, and how to maintain a system using package managers and automation.

Question: Let's proceed!

> Answer: No answer needed

***

## Task 2: Deploy Your Linux Machine

Similar to Part 2, you must deploy the attached virtual machine and connect to it remotely via SSH. This provides a sandbox environment to safely test out commands that modify system behavior and processes.

```bash
# Connect to the remote machine
ssh tryhackme@IPADDRESS
```

Question: I've logged into the Linux Fundamentals Part 3 machine using SSH and have deployed the AttackBox successfully!

> Answer: No answer needed

***

## Task 3: Terminal Text Editors

When interacting with a remote server, you rarely have a graphical text editor like Notepad. Instead, you must edit files directly inside the terminal. `nano` is the most beginner-friendly terminal text editor, allowing you to quickly create, modify, and save files. `vim` is a much more advanced alternative that features deep customizability and syntax highlighting, making it a popular choice for developers and system administrators.

```bash
# Open or create a file named "hello.txt" using nano
nano hello.txt

# Common nano shortcuts:
# Save file: Ctrl + O
# Exit nano: Ctrl + X
# Search for text: Ctrl + W
```

Question: Create a file using Nano

> Answer: No answer needed

Question: Edit "task3" located in tryhackme's home directory using nano. What is the flag?

> Answer: THM{TEXT\_EDITORS}

***

## Task 4: General/Useful Utilities

Transferring files to and from a remote server is a daily requirement. `wget` is used to download files directly from web servers via HTTP. `scp` (Secure Copy) allows you to securely transfer files over SSH. Additionally, Python 3 has a built-in module that can instantly turn your current directory into a lightweight web server, which is highly useful for quickly serving files to other machines on the network.

```bash
# Download a file from a web server
wget http://67.67.67.67:67/.flag.txt

# Start a simple web server on port 8000 using Python 3
python3 -m http.server 8000

# Securely copy a file from your local machine to a remote server
scp important.txt tryhackme@10.10.10.10:/home/tryhackme/
```

Question: Ensure you are connected to the deployed instance.

> Answer: No answer needed

Question: Now, use Python 3's "HTTPServer" module to start a web server in the home directory of the "tryhackme" user on the deployed instance.

> Answer: No answer needed

Question: Download the file onto the TryHackMe AttackBox. What is the flag?

> Answer: THM{WGET\_WEBSERVER}

Question: Use Ctrl + C to stop the Python3 HTTPServer module once you are finished.

> Answer: No answer needed

***

## Task 5: Processes 101

Every running program on a Linux machine is called a process, and each one is assigned a unique Process ID (PID). Using `ps aux` or `top` allows you to view these running processes, their resource usage, and the user executing them. You can use the `kill` command to terminate unresponsive processes. Additionally, `systemctl` is used to manage background services, allowing you to start, stop, or enable services to run automatically when the system boots.

```bash
# View all running processes on the system
ps aux

# View real-time interactive process statistics
top

# Gracefully terminate a process with PID 1337
kill -SIGTERM 1337

# Start a service (like a web server) and enable it to run on boot
systemctl start apache2
systemctl enable apache2

# Bring a backgrounded process back to the foreground
fg
```

Question: Read me!

> Answer: No answer needed

Question: If we were to launch a process where the previous ID was 300, what would the ID of this new process be?

> Answer: 301

Question: If we wanted to cleanly kill a process, what signal would we send it?

> Answer: SIGTERM

Question: Locate the process that is running on the deployed instance. What flag is given?

> Answer: THM{PROCESSES}

Question: What command would we use to stop the service "myservice"?

> Answer: systemctl stop myservice

Question: What command would we use to start the same service on boot-up of the system?

> Answer: systemctl enable myservice

Question: What command would we use to bring a previously backgrounded process back to the foreground?

> Answer: fg

***

## Task 6: Maintaining Your System with Automation

System administrators frequently need to schedule recurring tasks, such as database backups or system updates. Linux uses a process called `cron` to handle this automation. Users can edit their `crontab` file to specify exactly when a command should run using a strict formatting structure based on minutes, hours, days, and months.

```bash
# Edit the crontab file for the current user
crontab -e

# Example cron syntax: Run a backup script every day at 2:00 AM
# 0 2 * * * /home/user/backup.sh
```

Question: Ensure you are connected to the deployed instance and look at the running crontabs.

> Answer: No answer needed

Question: How frequently does this crontab run?

> Answer: @reboot

***

## Task 7: Maintaining Your System with Package Management

Instead of manually downloading individual installers, Linux uses package managers like `apt` to automatically download, install, and update software from centralized repositories. When you need a tool that is not in the default repository, you can add third-party repositories to your system's source list. The `apt` system also uses GPG keys to verify the integrity and origin of the software before it is installed.

```bash
# Update the local list of available packages
apt update

# Install a specific software package
apt install sublime-text

# Remove a software package
apt remove sublime-text
```

Question: Since TryHackMe instances do not have an internet connection...this task only requires you to read through the material.

> Answer: No answer needed

***

## Task 8: Maintaining Your System with Logs

Monitoring logs is a critical defensive security skill. Most system and application logs are stored in the `/var/log` directory. Reviewing files like the `apache2` access logs allows you to see exactly which IP addresses are connecting to your web server and what files they are requesting. This is how defenders detect unauthorized access attempts or troubleshoot application errors.

```bash
# Navigate to the system log directory
cd /var/log

# View the Apache access logs using the 'less' utility
less apache2/access.log
```

Question: Look for the apache2 logs on the deployable Linux machine.

> Answer: No answer needed

Question: What is the IP address of the user who visited the site?

> Answer: 10.9.232.111

Question: What file did they access?

> Answer: catsanddogs.jpg

***

## Conclusion

This concludes the Linux Fundamentals series. By progressing from basic filesystem navigation to advanced process management, automation, and logging, we now have the foundational skills required to operate comfortably within a Linux environment. Practice is key, as these commands will become muscle memory the more we use them during security assessments and server administration.
