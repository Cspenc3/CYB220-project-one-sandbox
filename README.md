# CYB-220 Project One – Virtual Systems and Networking Concept Lab

This project is a virtualized **sandbox network lab** completed as part of SNHU’s **CYB-220: Network Security** course.  
The goal of the lab is to safely test and document security-related changes in a simulated environment before applying them to a live production network.

The lab was built using **GNS3** (via InfoSec Learning) and focuses on:

- Configuring **Windows Group Policy** to harden local systems
- Setting **static IP addressing** and gateway configuration across multiple hosts
- Verifying **connectivity** and policy enforcement in a sandboxed virtual environment
- Explaining the **benefits and drawbacks of virtualization** for security work

---

## ⚙️ Lab Environment Overview

**Tools & Platforms**

- GNS3 (InfoSec Learning Lab environment)
- Windows 10 / Windows Server 2016 (virtual machines)
- Basic router/switch topology with kiosk and admin networks

**High-Level Topology**

- **Network_Router**
  - `Gig0/0` → 192.168.1.1/24 (Kiosk network)
  - `Gig0/1` → 192.168.2.1/24 (Admin network)
- **Kiosk Network (192.168.1.0/24)**
  - `PC1_Kiosk` – 192.168.1.101 / 24, GW 192.168.1.1
  - `PC2_Kiosk` – 192.168.1.102 / 24, GW 192.168.1.1
  - `PC3_Kiosk` – 192.168.1.103 / 24, GW 192.168.1.1
- **Admin Network (192.168.2.0/24)**
  - `PC1_Admin` – 192.168.2.100 / 24, GW 192.168.2.1
  - `Server_Domain` – 192.168.2.101 / 16 (per scenario), GW 192.168.2.1

This setup mimics a small organization with separate kiosk and administrative segments behind a central router.

---

## 🛡️ Group Policy Hardening

As part of the concept brief, I configured several **local Group Policy** settings to enforce security controls on Windows systems. All changes were done in a **sandboxed** virtual environment.

Key GPO changes:

1. **User Account Control (UAC) behavior**
   - Adjusted the UAC prompt behavior for administrators to enforce elevation awareness.

2. **Interactive Logon Warning Banner**
   - Policy: `Interactive logon: Message text for users attempting to log on`
   - Value:  
     > `Warning: You are about to do something that is not authorized.`

3. **Password Policy**
   - Policy: `Minimum password length`
   - Value: `8` characters
   - Enforces stronger local password requirements.

4. **Desktop Background Restriction**
   - Policy: `Prevent changing desktop background`
   - Set to: **Disabled**
   - Comment:  
     > `You are not authorized to change this setting`
   - Demonstrates using GPO to control user personalization and enforce standard images.

5. **Audit Logon Events**
   - Policy: `Audit logon events`
   - Set to: **Enabled** (Success and/or Failure depending on OS options)
   - Enables visibility into authentication activity at the host level.

6. **Lock Screen / Logon Image**
   - Policy: `Force a specific default lock screen and logon image`
   - Set to: **Enabled**
   - Image path: `C:\Windows\SNHULogo.jpg`
   - Used to show organizational branding and security messaging at logon.

7. **Windows Firewall Profile**
   - Policy: `Windows Defender Firewall Properties`
   - Firewall State: **On**
   - Inbound connections: **Allow**
   - Outbound connections: **Allow**
   - Demonstrates basic firewall profile configuration at the host level.

All of these changes were verified via screenshots from the **Local Group Policy Editor**.

---

## 🌐 Network Configuration Summary

Each host and the router interfaces were manually configured based on the project scenario.

### Router – `Network_Router`

- `GigabitEthernet0/0`  
  - IP: `192.168.1.1`  
  - Mask: `255.255.255.0`
- `GigabitEthernet0/1`  
  - IP: `192.168.2.1`  
  - Mask: `255.255.255.0`

### Kiosk Hosts

- `PC1_Kiosk`  
  - IP: `192.168.1.101`  
  - Mask: `255.255.255.0`  
  - Gateway: `192.168.1.1`

- `PC2_Kiosk`  
  - IP: `192.168.1.102`  
  - Mask: `255.255.255.0`  
  - Gateway: `192.168.1.1`

- `PC3_Kiosk`  
  - IP: `192.168.1.103`  
  - Mask: `255.255.255.0`  
  - Gateway: `192.168.1.1`

### Admin Host

- `PC1_Admin`  
  - IP: `192.168.2.100`  
  - Mask: `255.255.255.0`  
  - Gateway: `192.168.2.1`

### Domain / Server

- `Server_Domain`  
  - IP: `192.168.2.101`  
  - Mask: `255.255.0.0` (as specified by scenario)  
  - Gateway: `192.168.2.1`

These configurations were validated using ping tests between hosts on both networks and across the router.

---

## 📸 Screenshots Included

This repo (or folder) includes screenshots for:

- **Group Policy Configurations**  
  - UAC behavior  
  - Logon message text  
  - Password policy  
  - Desktop background restriction  
  - Audit logon events  
  - Lock screen/logon image  
  - Firewall profile settings

- **Network Settings**  
  - Router interface configuration (`show ip interface brief` or GUI equivalent)  
  - IPv4 properties from each Windows host  
  - Server network configuration

All screenshots align with the CYB-220 Project One rubric requirements.

---

## 🧠 Conceptual Justification – Why Use Virtualization?

### Benefits of Virtualization for Sandboxing

- **Safe testing environment:**  
  Changes to Group Policy, network settings, and security controls can be tested without impacting real users or production services.

- **Easy reset and rollback:**  
  Virtual machines can be snapshotted and reverted, allowing fast recovery from misconfigurations or failed experiments.

- **Cost-effective:**  
  Multiple virtual systems can run on a single physical host, reducing hardware costs compared to building a full physical lab.

- **Reproducible labs:**  
  Instructors and analysts can share preconfigured virtual topologies so others can reproduce the same tests and scenarios.

### Drawbacks of Virtualization for Sandboxing

- **Performance overhead:**  
  Virtual machines share physical hardware and can suffer from CPU, memory, or disk contention if not properly resourced.

- **Reduced hardware realism:**  
  Some low-level hardware behavior, driver issues, or timing-sensitive attacks may not appear the same way they would on bare metal.

- **Complexity and management:**  
  Hypervisors, virtual switches, snapshots, and storage all add management overhead and require proper configuration and monitoring.

- **False sense of isolation if misconfigured:**  
  Poorly isolated virtual networks can still expose the host system or other VMs to risk if security boundaries aren’t carefully enforced.

### Other Uses of Virtualization Beyond Sandboxing

- **Server consolidation:**  
  Organizations can host multiple virtual servers (web, database, application) on fewer physical machines, improving utilization and simplifying backups.

- **Training and certification labs:**  
  Students and professionals can build entire practice environments (AD, Linux servers, firewalls, SIEM tools) without needing physical gear.

- **Disaster recovery and high availability:**  
  Virtual machines can be replicated, moved, or restored quickly to alternate hosts or data centers during outages.

---

## 📚 What I Learned

Through this project, I practiced:

- Applying **Group Policy** to enforce host security settings
- Configuring **static IP addresses and gateways** on client and server systems
- Understanding how **virtual networks** behave in a sandboxed environment
- Documenting changes clearly for a **system administrator audience**
- Explaining the **practical tradeoffs** of virtualization for cybersecurity work

This lab is one of the building blocks in my journey toward more advanced **network security**, **Windows hardening**, and **virtual lab design**.

---

## 🔗 Related Work

- CYB-220 – ACL & email server lab (Packet Tracer)
- Future labs: firewall rules, IDS/IPS concepts, and expanded virtual SOC environments.


Add initial project README
