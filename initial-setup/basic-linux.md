---
description: Basic Linux command practice
icon: code
---

# Basic Linux

Date: February 9, 2026

## 1. Introduction and Context

Although I've been exposed to some Linux commands in my courses, I'm nowhere near "fluent" at the moment so I decided to get a quick refresher. In today's page, I'm going to be practicing some basic yet essential Linux commands.

## 2. System Maintenance and Updating

Before diving into lab work, I need to ensure the Kali instance is stable and has the latest package headers. This prevents issues where new tools fail to install due to outdated dependencies. Of course, before starting the rest of today's lab, I'm going to save a snapshot of the lab just in case. You can never be "too safe" in cybersecurity after all.

### The Update Workflow

I use the following one-liner to refresh the package list and upgrade all installed packages:

```bash
sudo apt update && sudo apt full-upgrade -y
```

#### Breakdown:

* `sudo`: Executes the command with root (administrative) privileges.
* `apt update`: Downloads the latest package lists from the repositories. It doesn’t install anything; it just "checks" what’s new.
* `&&`: A logical operator that tells the terminal: "Only run the next command if the first one finished successfully."
* `full-upgrade`: This is preferred for Kali. It upgrades all packages and handles changing dependencies (adding or removing packages as needed).
* `-y`: The "Assume Yes" flag. It automatically agrees to the disk space usage prompt so the update doesn't pause.

### Cleaning Up

To keep the VM slim and remove old, unused packages after an upgrade:

```bash
sudo apt autoremove -y && sudo apt autoclean
```

#### Breakdown:

* `autoremove`: Removes packages that were automatically installed to satisfy dependencies for other packages but are now no longer needed. This keeps the VM "lean."
* `autoclean`: Clears out the local repository of retrieved package files (`.deb` files). It specifically removes files that can no longer be downloaded (old versions), freeing up disk space in `/var/cache/apt/archives`.

## 3. Navigation and File Management

Efficiently moving through the Linux filesystem is essential. I’m focusing on using absolute and relative paths, as well as maintaining an organized directory structure for my lab results.

### Essential Navigation

* `pwd`: Print Working Directory. Confirms exactly where I am before running scripts or moving files.
* `ls -la`: List all. The `-a` flag is vital for seeing hidden configuration files (like `.bashrc`), while `-l` provides details on permissions and file sizes.
* `cd [path]`: Moving between directories. I am practicing the use of `cd Desktop` to move up one level and `cd ~` to return home instantly.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Organizing the Lab

I made this folder using the following command:

```bash
mkdir -p ~/homelab/findings
```

#### Breakdown:

* `mkdir`: Create a new directory.
* `-p` (Parents): This flag is a massive time-saver. It creates the entire folder path at once, even if the parent folders (`homelab` or `findings`) don't exist yet.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

As you see in the screenshot above, the homelab folder and its subdirectory has been created.

### Managing Files

* `touch [filename]`: Creates an empty file. Useful for prepping log files.
* `mv [source] [destination]`: Used both for moving files and renaming them.
* `cp -r [source] [destination]`: Copies files. The `-r` flag is required to copy entire folders (recursive).
* `rm -rf [target]`: Deletes files or folders. I use the `-f` flag to force deletion without being prompted for every single file.

I've looked over these commands again however I'm not going to use them today.

## 4. Searching and Filtering (The Power Moves)

As my homelab grows, I’ll be dealing with more data. These commands allow me to find specific strings of text or files buried deep in the system.

#### Searching within Files:&#x20;

`grep` is used to search for specific text patterns inside files.&#x20;

```bash
grep "error" /var/log/syslog
```

#### Breakdown:

* `"error"`: The specific string I am looking for.
* `/var/log/syslog`: The file I am searching in.
* Common Flag `-i`: Makes the search case-insensitive (so it finds "Error", "ERROR", and "error").
* Common Flag `-r`: Recursive search. Searches every file in a directory.

I also tried the following two commands, the first one shows me the port num assigned to https and the second command shows me how many cpu cores I assigned and what they are.

```bash
grep "https" /etc/services
cat /proc/cpuinfo | grep "model name"
```

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

#### Finding Files:&#x20;

When I know a file exists but I don't know where it is, I use `find`.

```bash
find /etc -name "*.conf"
```

Breakdown:

* `/etc`: The starting directory for the search.
* `-name`: Tells the command I'm searching by filename.
* `"*.conf"`: A wildcard search for any file ending in `.conf`.

Anyways, I have a big presentation tomorrow for my programming module so that's all for today's lab! So far in today's lab, I learned basic file navigation, management and searching. Although these commands are simple and basic, I'll be running them a lot in the future!
