---
icon: terminal
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 3 - Initial Cowrie Setup

**Date:** 24.05.2026

**Core Concept**: Host Hardening, Deception Technology, and Containerized Sandboxing

Deploying our first active honeypot requires a solid, hardened foundation. Because our deception service must occupy standard interactive ports to act as believable bait, we cannot leave our real administrative access on its default configurations. Today we provisioned the Linux Honeypot VM, patched it using a generalized boot script, securely migrated our SSH interface using a custom script, and deployed a sandboxed Cowrie honeypot.

***

## Section 1: VM Provisioning and Baseline Patching

We deployed a lightweight Ubuntu 22.04 LTS instance using the student free tier **B2ats v2** size (2 vCPU, 1GB RAM) within the target subnet (`snet-target`). We selected a **30 GB Standard SSD** to keep storage costs at a minimum (around $2.40/month) while providing plenty of space for log retention.

To ensure every VM in our lab starts from a secure, standardized baseline, we pasted a custom initialization script into the Azure **Custom data** field during creation. This script automatically runs non-interactive system updates, installs essential troubleshooting tools, configures a baseline local firewall, and cleans up the package cache on the very first boot.

**VM Initialization Script (Custom Data):**

```bash
#!/bin/bash
# Basic VM Initialization Script

# Ensure the script is running as root
if [ "$EUID" -ne 0 ]; then
  echo "Error: Please run this script as root or using sudo." >&2
  exit 1
fi

# Redirect output to a log file and terminal
exec > >(tee -a /var/log/vm-init.log) 2>&1
echo "Starting VM Initialization..."

# Suppress interactive prompts
export DEBIAN_FRONTEND=noninteractive

echo "Updating package lists..."
apt-get update -y

echo "Upgrading system packages..."
apt-get dist-upgrade -y

echo "Installing common troubleshooting tools..."
apt-get install -y curl wget iproute2 unzip nano htop ufw git build-essential

echo "Configuring basic firewall safety..."
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw --force enable

echo "Cleaning up package cache..."
apt-get autoremove -y
apt-get clean

echo "Initialization Complete!"
```

***

## Section 2: Scripted SSH Port Migration

Before installing Docker or Cowrie, we had to migrate the native administrative SSH service away from port 22 to prevent conflicts. Because modern Ubuntu utilizes an aggressive systemd socket activation feature (`ssh.socket`) that often ignores standard configuration file edits, manual port changes can silently fail.

To guarantee success and bypass this interference, we created and executed a dedicated SSH migration script that disables the socket, forces the new port override, and updates the local firewall simultaneously.

1.  **Initial Login:** We connected to the fresh VM using the default port:

    ```bash
    ssh labadmin@<VM_Public_IP>
    ```
2.  **Execute Migration Script:** We created a script named `ssh-migrate.sh` to permanently move the SSH service to port 64295:

    ```bash
    #!/bin/bash
    # Create an override file that forces Port 64295
    echo "Port 64295" | sudo tee /etc/ssh/sshd_config.d/99-custom-port.conf

    # Completely kill the Ubuntu socket activation
    sudo systemctl stop ssh.socket
    sudo systemctl disable ssh.socket
    sudo systemctl daemon-reload

    # Ensure local firewall is open for the new port
    sudo ufw allow 64295/tcp

    # Restart the traditional SSH service
    sudo systemctl restart ssh

    # Show exactly what ports SSH is listening on now
    sudo ss -tulpn | grep ssh
    ```
3. **Verification:** The script's final output confirmed SSH was actively listening on `*:64295`. We then opened our Azure Network Security Group (`nsg-target-honeypots`), added an inbound rule allowing TCP 64295 from our Home IP, and successfully reconnected to the VM on the new port.

***

## Section 3: Docker Environment Setup

To keep our honeypot isolated from the host OS, we deployed Cowrie as a containerized microservice.

1.  **Install Docker:**

    ```bash
    sudo apt install docker.io docker-compose-v2 -y
    ```
2.  **Enable Docker Daemon:**

    ```bash
    sudo systemctl enable docker
    sudo systemctl start docker
    ```

***

## Section 4: Deploying Containerized Cowrie

With port 22 completely free and Docker running, we deployed the honeypot.

1.  **Directory Structure:** We created a dedicated directory for our persistent logs:

    ```bash
    mkdir -p ~/cowrie/var/log/cowrie
    cd ~/cowrie
    ```
2.  **Docker Compose Configuration:** We created a `docker-compose.yml` file to bind the honeypot container directly to the host's standard SSH and Telnet ports:

    ```bash
    version: '3.8'
    services:
      cowrie:
        image: cowrie/cowrie:latest
        container_name: cowrie_honeypot
        restart: always
        ports:
          - "22:2222"
          - "23:2223"
        volumes:
          - ./var/log/cowrie:/cowrie/cowrie-git/var/log/cowrie
    ```
3.  **Launch the Container:**

    ```bash
    sudo docker compose up -d
    ```

***

## Section 5: Testing the Trap

We verified the deployment by simulating an attacker from our local machine:

* **Honeypot Login:** We connected via `ssh root@<VM_Public_IP> -p 22` and were immediately dropped into the simulated Cowrie filesystem.
*   **Log Verification:** Back on our administrative session (port 64295), we verified the attack was recorded:

    ```bash
    cat ~/cowrie/var/log/cowrie/cowrie.json
    ```

***

## Section 6: Version Control and Code Repositories

To maintain professional documentation standards, both the baseline initialization script (`general_vm_init.sh`) and the SSH migration script (`migrate_ssh.sh`) have been uploaded to a public GitHub repository.

To demonstrate proper Operational Security (OPSEC) while sharing our configurations, we also published a scrubbed template of the `docker-compose.yml` file and a sanitized sample of the `cowrie.json` event log (completely removing personal public IP addresses and Azure subscription identifiers).

***

## Conclusion

Day 3 is complete. The Linux Honeypot is actively running on a free B2ats v2 tier, and our administration channel is securely separated from the honeypot software. By engineering custom boot and migration scripts, we ensured a clean, repeatable deployment while bypassing cloud-init and socket activation interference.&#x20;

Tomorrow, we move into Day 4: cloaking cowrie to get better attacker data.
