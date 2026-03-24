---
icon: magnifying-glass
cover: >-
  https://cdn.discordapp.com/attachments/1031890307979149332/1483977967347433593/image.png?ex=69c1d3ae&is=69c0822e&hm=25522067af4b6d5ef6422a90b24711fb0f5e62d1bd0b48c156779b9ffc173adc
coverY: 0
coverHeight: 138
---

# Day 3 - Initial Reconnaissance

**Date:** 23.03.2026

**Focus:** Repository Setup, Passive Discovery, and Active Reconnaissance

Now that the infrastructure is isolated and the victim node (**Austo-College**) is live, Day 3 marks the beginning of **Phase 1: The Tug of War**. In this session, we step into the role of the attacker. Although we technically know the static IP assigned yesterday, we will proceed as if we are blind to the network layout to practice professional methodology.

***

## Section 1: Documentation Environment Setup

Before launching any attacks, we must ensure our documentation pipeline is ready. We will clone the central lab repository into the directory established during Day 1 and set up a structured folder for scan logs.

* **Action:** Navigate to the lab directory, clone the repo, and create a reconnaissance folder.
* **Location:** `~/Desktop/SOC_Lab/reconnaissance`

```bash
# Navigate to the existing SOC_Lab folder on the Desktop
cd ~/Desktop/SOC_Lab

# Clone the SOC-Homelab repository into the current directory
git clone [https://github.com/MisterOctal/SOC-Homelab](https://github.com/MisterOctal/SOC-Homelab) .

# Create a sub-folder for storing scan data
mkdir reconnaissance
```

***

## Section 2: Network Host Discovery

The first tactical objective is to "find" the target on the isolated `VMnet1` subnet. We check our own IP first to identify our local range.

* **Check Own IP:** `ip addr` (Confirmed Kali is on the **`192.168.138.x`** subnet).
* **Initial Ping Sweep:** `sudo nmap -sn 192.168.138.0/24`
* **Discovery Hurdle:** During the initial sweep, Nmap reported that **every IP address in the range was "up."** This is a common artifact in virtualized host-only networks where the hypervisor responds to all ARP requests.
* **Refined Discovery:** To bypass the noise, we targeted the expected static range and used an ARP check to confirm the hardware address.
* **Result:** Nmap/ARP successfully identifies the live target at **`192.168.138.100`** .

***

## Section 3: Initial Port Scanning

With the target IP confirmed as `192.168.138.100`, we proceed to map the available attack surface. We assume the system is fully operational and "up," so we run comprehensive scans to capture all listening services.

**The "Quick Look" Scan**

To get immediate results and identify common entry points, we run a fast scan.

```bash
# Fast scan to identify the top 100 most common ports
nmap -F 192.168.138.100
```

**The Comprehensive Scan & Logging**

To ensure no custom services are missed, we follow up with a full scan. We use the `-oN` flag to save the output specifically into our newly created reconnaissance folder for documentation.

```bash
# Scanning all ports with service version detection
# Output is saved to the 'reconnaissance' folder as initial_recon.txt
sudo nmap -p- -sV -sS -T4 192.168.138.100 -oN reconnaissance/initial_recon.txt
```

***

## Section 4: Observations

The scans confirm that the target system is highly talkative. A wide variety of services are active and responding to probes across multiple protocols (TCP/UDP). **Everything is up and reachable.** According to the captured `initial_recon.txt`, the attack surface is extremely broad:

* **Legacy Protocols:** Insecure services like Telnet (23), Rlogin (513), and FTP (21) are active.
* **Web & Databases:** Apache 2.2.8 is running on port 80, alongside MySQL (3306) and PostgreSQL (5432).
* **High-Risk Findings:** A notable "Ingreslock" bindshell was detected on **port 1524**, which typically provides immediate root access without credentials.
* **Infrastructure Services:** DNS (53), RPC (111), and SMB (139/445) are all exposed.

The initial response indicates a "Swiss Cheese" security posture that is fully operational and ready for a more granular inspection.

***

## Summary of Progress

Today's session successfully established the connection between the attacker and the target while ensuring all data is captured in the lab repository.

**Key Achievements:**

* **Workspace Sync:** Cloned the `SOC-Homelab` repository and initialized the `reconnaissance` folder.
* **Reconnaissance:** Successfully "discovered" the target IP at `.100` despite virtual network noise.
* **Data Persistence:** Logged the full service version scan to `initial_recon.txt`.
* **Service Mapping:** Captured version banners for all detected active services.

**Note:** A copy of `initial_recon.txt` has been pushed onto GitHub as well.

**Next Steps (Day 4):** With the target fully "discovered" and all services confirmed as operational, we will transition to **In-Depth Analysis**. Day 4 will focus on an exhaustive breakdown of the services logged in `initial_recon.txt`, researching specific software versions for known CVEs.
