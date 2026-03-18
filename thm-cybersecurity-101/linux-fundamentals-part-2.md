---
icon: linux
---

# Linux Fundamentals Part 2

Date: 17.03.2026

Room Category: Walkthrough

Core Concept: Advancing Linux command-line skills, including remote access via SSH, using command flags, managing files, understanding permissions, and exploring common system directories.

Building on the absolute basics, this section introduces how to remotely connect to a server and significantly expands your ability to manipulate the filesystem. Understanding how to manage permissions and knowing where critical log files and system configurations are stored are essential skills for navigating any compromised or audited system.

***

## Task 1: Introduction

This room transitions from the in-browser terminal used in Part 1 to connecting remotely to a machine. It unlocks the potential of previously learned commands by introducing arguments and flags, covers creating and moving files, introduces the Linux permission model, and explores standard directory structures.

Question: Let's proceed!

> Answer: No answer needed

***

## Task 2: Accessing Your Linux Machine Using SSH

Secure Shell (SSH) is the standard protocol for connecting to and interacting with the command line of a remote Linux machine. Any input sent or output received over an SSH connection is encrypted, ensuring that anyone monitoring the network cannot read the commands or passwords being transmitted.

<figure><img src="../.gitbook/assets/image (25).png" alt="" width="563"><figcaption></figcaption></figure>

```bash
# Standard SSH connection syntax
# ssh username@ip_address
ssh tryhackme@IPADDRESS
```

Question: I've logged into the Linux Fundamentals Part 2 machine using SSH!

> Answer: No answer needed

***

## Task 3: Introduction to Flags and Switches

Most Linux commands accept arguments - identified by a hyphen (like `-a` or `--help`) that alter or extend the command's default behavior. For instance, `ls` only shows visible files, but `ls -a` reveals hidden files (which start with a period). If you are ever unsure of what flags a command accepts, you can read its manual using the `man` command.

```bash
# View the manual page for the 'ls' command
man ls

# List all files, including hidden ones
ls -a
```

Question: Explore the manual page of the ls command.

> Answer: No answer needed

Question: What directional key will be used to navigate down the manual page?

> Answer: down

Question: What flag will be used to display the output in a human-readable format?

> Answer: -h

***

## Task 4: Filesystem Interaction Continued

This task introduces the commands needed to actively modify the filesystem rather than just reading it. You can create blank files using `touch`, make new directories with `mkdir`, remove them using `rm`, and duplicate or relocate them using `cp` (copy) and `mv` (move). Because Linux does not rely on file extensions (like `.txt` or `.exe`) to determine what a file does, the `file` command is critical for identifying the actual contents of an unknown file.

```bash
# Create a new, blank file
touch new_note

# Create a new directory
mkdir new_folder

# Move or rename a file
mv new_note new_folder/

# Determine the actual type of a file
file unknown1
```

Question: How would you create the file named "newnote"?

> Answer: touch newnote

Question: On the deployable machine, what is the file type of "unknown1" in "tryhackme's" home directory?

> Answer: ASCII text

Question: How would we move the file "myfile" to the directory "myfolder"?

> Answer: mv myfile myfolder

Question: What are the contents of this file?

> Answer: THM{FILESYSTEM}

Question: Continue to apply your knowledge and practice the commands from this task.

> Answer: No answer needed

***

## Task 5: Permissions 101

Linux relies heavily on a strict permission model. When listing files with `ls -l`, you can see who owns a file, which group it belongs to, and the specific read (`r`), write (`w`), and execute (`x`) permissions assigned to the owner, the group, and everyone else. To interact with files owned by someone else, you can use the `su` (substitute user) command to switch accounts, provided you know their password.

```bash
# View detailed permissions and ownership of files
ls -l

# Switch to another user account (prompts for their password)
su user2
```

Question: On the deployable machine, who is the owner of "important"?

> Answer: user2

Question: What would the command be to switch to user 2?

> Answer: su user2

Question: Now switch to this user "user2" using the password "user2". Read "important". What is the flag?

> Answer: THM{SU\_USER2}

***

## Task 6: Common Directories

Understanding the standard Linux directory structure saves a lot of time during enumeration.

* `/etc` stores core system configuration files, including the `passwd` and `shadow` files that manage user accounts and password hashes.
* `/var` stores variable data, most notably application and system log files in `/var/log`.
* `/root` is the dedicated home directory for the root (administrative) user.
* `/tmp` is a temporary storage directory that is cleared upon reboot. It is highly useful during assessments because any user can typically write files to it.

```bash
# Navigate to the system logs directory
cd /var/log

# View the system configuration directory
ls /etc
```

Question: What is the directory path that we would expect logs to be stored in?

> Answer: /var/log

Question: What root directory is similar to how RAM on a computer works?

> Answer: /tmp

Question: Name the home directory of the root user.

> Answer: /root

***

## Conclusion

This room bridged the gap between basic observation and active system manipulation. Remote access via SSH, utilizing command flags via the manual pages, modifying the filesystem, and understanding the Linux permission matrix are foundational capabilities for managing or auditing any Linux environment.
