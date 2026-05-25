---
icon: eye-slash
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 4 - Cowrie Cloaking I

**Date:** 25.05.2026

**Core Concept**: Advanced Deception Engineering and Host Cloaking

Today, we moved from basic honeypot deployment to high-fidelity deception. By abandoning the unstable `docker exec` method in favor of a Host-First deployment model, we successfully injected a custom, believable persona into our containerized environment. To ensure version control and environment recovery, we pushed our updated `docker-compose.yml` and the `fake_log_generator.py` script to our GitHub repository.

***

## Section 1: The Host-First Cloaking Method

To achieve full control over the honeypot's personality, we moved all filesystem generation logic to the VM host.

1. **System Generator Creation:** We vibecoded a `fake_log_generator.py`. This script builds the honeypot internal structure on the VM host. It automates directory creation, populates a realistic `/etc/passwd` file with fake users like `dbadmin` and `josem`, and synthesizes `auth.log` entries spanning five days of activity to simulate a lived-in environment.
2.  **Mounting Strategy:** We updated our `docker-compose.yml` to treat the container as a blank shell that adopts the filesystem we provide directly from the host.

    ```yml
    volumes:
      - /home/labadmin/cowrie/var/log/cowrie:/cowrie/cowrie-git/var/log/cowrie
      - /home/labadmin/cowrie/honeyfs/etc/passwd:/cowrie/cowrie-git/honeyfs/etc/passwd
      - /home/labadmin/cowrie/honeyfs/var/log:/cowrie/cowrie-git/honeyfs/var/log
      - /home/labadmin/cowrie/honeyfs/home/dbadmin:/cowrie/cowrie-git/home/dbadmin
    ```

***

## Section 2: Implementing Deception Loot

We populated the environment with honey tokens to trigger attacker interest and simulate a production database server:

* **Dynamic Loot Management:** We can modify sensitive files like `.env` directly in `~/cowrie/honeyfs/home/dbadmin/` without restarting the container. The honeypot presents the updated files to attackers in real-time.
* **Credential Management:** We implemented a mechanism to control acceptable passwords for the honeypot. Changes to our local credential files require a simple `docker compose restart` to take effect.

***

## Section 3: Configuration and Expansion

We enabled Telnet to catch legacy botnet traffic and hardened our SSH banner to mimic an older Ubuntu release. By keeping the configuration logic local to the VM host and only pushing the generator and deployment scripts to GitHub, we maintain a clean separation between sensitive honeypot configs and our deployment code.

***

## Conclusion

Day 4 has established a stable, cloaked deception environment. By leveraging a custom Python generator and host-level volume mounting, we have gained the ability to modify our honeypot persona and loot in real-time without dependency issues. Our trap is now fully operational, cloaked, and version-controlled, waiting for incoming traffic.

Tomorrow, we will expand our cloaking techniques even further.
