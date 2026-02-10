---
description: Initial Kali Linux Setup and Customization
icon: hammer
---

# Kali Linux Setup

Date: February 2, 2026

## 1. Lab Environment & Hardware

Before diving into the Kali Linux installation, it's important to document the hardware environment. This ensures that any performance issues or configuration steps are understood within the context of the host machine's capabilities.

### Host Machine Specifications

My current setup is a reliable, 6-year-old laptop that has been with me since I was in year 8. It handles both my college coursework as well as occasional gaming.

* **Memory**: 16 GB RAM
* **Storage**:
  * **HDD**: 1 TB BarraCuda Hard Drive (Lab Host)
  * **SSD**: 256 GB Kingston Solid State Drive
* **GPU**: GTX 1650 With Max Q Design
* **CPU**: Intel Core i7-9750H CPU (2.60 GHZ)
* **Monitors**:
  * **Primary**: Laptop Monitor&#x20;
  * **Secondary**: Dell P2016 20" Monitor&#x20;

Although SSDs have superior performance, I am hosting my lab on my **HDD** due to limited SSD storage and wanting to reserve my SSD for Windows and coursework. All my labwork is stored in D:\Homelab.

It is also important to note that I am getting a new laptop near the end of the year so it's important I'm able to safely transition my lab onto a new environment.&#x20;

### Virtualization

Thanks to my HND coursework, I already have both VMware and VirtualBox installed on my machine. Although VirtualBox is a great tool, I've chosen VMware as my primary hypervisor for this Kali setup for a few reasons:

* **Stability**: In my experience, VMware handles hardware passthrough (like Wi-Fi adapters) more consistently and easily.
* **Performance**: VMware's graphics acceleration and memory management are essential on this hardware, this is important if I ever want to run resource-heavy security tools.
* **Industry Standard**: VMware is the more prevalent in enterprise environments. Mastering it now aligns with my goal of joining the cyber industry in the future.

## 2. Kali Linux Installation & Setup

Firstly, I simply downloaded the Kali Linux ISO file from [kali.org](https://www.kali.org/get-kali/#kali-installer-images). Afterwards, I created a new linux virtual machine and chose my OS version as 'Debian 10.x 64-bit' as Kali is based on debian.

### Virtual Hardware Configuration

Before launching the installer, I modified the default VM settings to do the following:

* **Memory Allocation (RAM)**
  * **Setting**: 2GB (2048 MB)
  * **Why**: Although Kali can run on 1 GB, I chose 2 GB as its the "sweet spot" for my lab. It provides enough overhead for the Xfce desktop and multiple terminal windows without causing any issues to my host machine.
* **Network Adapter Configuration**
  * **Setting**: NAT (Network Address Translation)
  * **Why**: I've learned in my HND studies that a NAT will allow my Kali VM to share my host's IP address to access external networks. Additionally, the VMware NAT ensures my VM is not directly exposed to my home network.
* **Processor**
  * **Setting**: 2 Cores
  * **Why**: 2 cores will prevent the UI from "stuttering" during high-CPU tasks like password cracking and full system scans. It should also suffice for multi-tasking.

Lastly, I gave my OS 40 GB worth of storage, this ensures there is plenty of space for the OS and any additional software I may add.

### Launching and Configuring Kali Linux

Upon booting up the VM, I was greeted by a Kali Linux installer menu, I decided to select the Graphical install option as I still want to use the Kali Linux GUI for homelab documentation screenshots and ease of use.

Afterwards, I selected the language I want my system to be, in this case I just want to use UK English. Then Kali began automatically configuring the network and devices, before asking me to enter my hostname. For this lab, I have decided to name my kali MisterOctal.

During the network setup phase, the installer also asked me for a **domain name**. This is a key networking concept I'll be seeing again in my Security+ studies. However, for my homelab I simply left the domain name blank. I may need to reconfigure this option if I wish to practice active directory attacks later down the line though.

Now, this is when the "juicy" part comes in. I was prompted on what tools I want to install for my Kali Linux VM. I decided to stay with Kali's default Xfce desktop environment as Xfce is lightweight and shouldn't cause any troubles.&#x20;

Additionally, I have selected the top 10 and default tools. I've chosen this route because, as a security professional, it is better to understand a few core tools deeply rather than having 600 tools installed that I'll never use. This also helps me keep my disk size manageable. I picked the standard kali installer so there's no option for the "large" tool package.

<figure><img src="../.gitbook/assets/image (6).png" alt="" width="563"><figcaption></figcaption></figure>

After this, I saved a snapshot of my Kali Linux to serve as a restore point in case of damage. That's all for today!
